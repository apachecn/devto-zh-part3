# 将所有对象从一个 S3 桶复制到另一个桶的 Python 代码

> 原文：<https://dev.to/sjehutch/python-code-to-copy-all-objects-from-one-s3-bucket-to-another-5hc2>

```
import boto3

s3_resource = boto3.resource('s3')

new_bucket_name = "targetBucketName"
bucket_to_copy = "sourceBucketName"

for key in s3.list_objects(Bucket=bucket_to_copy)['Contents']:
    files = key['Key']
    copy_source = {'Bucket': "bucket_to_copy",'Key': files}
    s3_resource.meta.client.copy(copy_source, new_bucket_name, files)
    print(files) 
```