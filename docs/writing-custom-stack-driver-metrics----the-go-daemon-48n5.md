# 编写定制的 StackDriver 指标——Go 守护进程

> 原文：<https://dev.to/tonymet/writing-custom-stack-driver-metrics----the-go-daemon-48n5>

[关于自定义指标设置，请参见第 1 部分](https://tonym.us/writing-custom-metrics-to-stackdriver-in-golang.html#writing-custom-metrics-to-stackdriver-in-golang)

## 2。使用 Golang 收集和收获定制指标

对于这个例子，我们只是跟踪行数。可以扩展
来解析成功与错误行，并将它们作为两个度量发送，例如
`backup-count`与`error-backup-count`

下面的代码创建了一个 5 秒钟的计时器，它计算文件的行数，并将计算结果发送给 stackdriver。堆栈驱动程序报告将允许您运行
统计数据，例如 avg() & max()，并允许您查看备份计数随时间的增长

```
package main

import (
        "bytes"
        "context"
        "fmt"
        "io"
        "log"
        "os"
        "time"

        monitoring "cloud.google.com/go/monitoring/apiv3"
        timestamp "github.com/golang/protobuf/ptypes/timestamp"
        metricpb "google.golang.org/genproto/googleapis/api/metric"
        monitoredres "google.golang.org/genproto/googleapis/api/monitoredres"
        monitoringpb "google.golang.org/genproto/googleapis/monitoring/v3"
)

const metricType = "custom.googleapis.com/photos/backup-count"

const (
    projectID = "YOUR-GCP-PROJECT-ID"
    instanceID = "INSTANCE-ID-FOR-TESTING"
    INTERVAL = 3
    zone = "us-east1-c"
)

func writeTimeSeriesValue(projectID, metricType string, value int) error {
        ctx := context.Background()
        c, err := monitoring.NewMetricClient(ctx)
        if err != nil {
                return err
        }
        now := &timestamp.Timestamp{
                Seconds: time.Now().Unix(),
        }
        req := &monitoringpb.CreateTimeSeriesRequest{
                Name: "projects/" + projectID,
                TimeSeries: []*monitoringpb.TimeSeries{{
                        Metric: &metricpb.Metric{
                                Type: metricType,
                        },
                        Resource: &monitoredres.MonitoredResource{
                                Type: "gce_instance",
                                Labels: map[string]string{
                                        "project_id":  projectID,
                                        "instance_id": instanceID,
                                        "zone":        zone,
                                },
                        },
                        Points: []*monitoringpb.Point{{
                                Interval: &monitoringpb.TimeInterval{
                                        StartTime: now,
                                        EndTime:   now,
                                },
                                Value: &monitoringpb.TypedValue{
                                        Value: &monitoringpb.TypedValue_Int64Value{
                                                Int64Value: int64(value),
                                        },
                                },
                        }},
                }},
        }
        log.Printf("writeTimeseriesRequest: %+v\n", req)

        err = c.CreateTimeSeries(ctx, req)
        if err != nil {
                return fmt.Errorf("could not write time series value, %v ", err)
        }
        return nil
}

// [END monitoring_read_timeseries_simple]

func LineCounter(r io.Reader) (int, error) {
        buf := make([]byte, 32*1024)
        count := 0
        lineSep := []byte{'\n'}

        for {
                c, err := r.Read(buf)
                count += bytes.Count(buf[:c], lineSep)

                switch {
                case err == io.EOF:
                        return count, nil

                case err != nil:
                        return count, err
                }
        }
}

// given interval, return the # of lines in the file
func WatchFile(interval time.Duration, filename string) <-chan int {
        handle, err := os.Open(filename)
        ticker := time.NewTicker(interval)
        sizeChan := make(chan int)
        go func() {
                for _ = range ticker.C {
                        lineCount, _ := LineCounter(handle)
                        sizeChan <- lineCount
                        if err != nil {
                                fmt.Println("error")
                        }
                        handle.Seek(0, 0)
                }
        }()
        return sizeChan
}

func main() {
        if len(os.Args) < 2 {
                fmt.Printf("%s FILENAME\n", os.Args[0])
                os.Exit(-1)
        }
        sizeChan := WatchFile(INTERVAL*time.Second, os.Args[1])
        for {
                select {
                case <-sizeChan:
                        curSize := <-sizeChan
                        fmt.Printf("Lines in %s : %d\n", os.Args[1], curSize)
                        writeTimeSeriesValue(projectID, metricType, curSize)
                }
        }
} 
```