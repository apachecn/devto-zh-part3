# Protobuf 日志

> 原文：<https://dev.to/x1957/protobuf-log-136g>

I have some data pushed regularly by mq, and now I have to make models based on them. Before, they were all received and passed, but never saved.
The pushed data is serialized by protobuf, so there is no need to deserialize it before logging.

1:
log directly,
pb1p2pb3pb4pb5 ... pbn
Finally, I found that I couldn't parse it, and the idea was beautiful at first. After all, there were only a few fields, so it was good to read them, but later I found that because pb might handle optional fields, I didn't know how long a message was.

Scheme 2:
add line break
pb1 \ n
Pb2 \ n
PB3 \ n
...
PBN \ n
Here, you are an idiot. PB may contain clutter such as line breaks (binary after all), so it is not feasible.

Scheme 3:
Put a length
length1 _ pb1length2 _ Pb2 ... lengthn _ pbn
in the header according to the way tcp sent packets before, so that when we parse, we can first read how many bytes there are, and then use these bytes to deserialize pbmessage.

And our length should be coded with pb at most, so as to avoid inconsistent processing
.

```
c.log.Write(proto.EncodeVarint(uint64(len(bs))))
c.log.Write(bs) 
```

Then here we use Rust to process the data.

[T2】 protobuf 【T3] This database is viewed and used by the most people, and its `CodedInputStream` `read_message` just deals with the data of our structure.

```
 pub fn read_message<M : Message>(&mut self) -> ProtobufResult<M> {
        let mut r: M = Message::new();
        self.merge_message(&mut r)?;
        r.check_initialized()?;
        Ok(r)
    } 
```

Rust's code for processing pb is as follows

```
mod message;
mod file;

#[macro_use]
extern crate slog;
extern crate slog_async;
extern crate slog_term;

use slog::Drain;

use clap::{App, Arg};
use message::Message;
use protobuf::CodedInputStream;
use std::fs;
use std::io::BufReader;

fn vist_dir(path: &str) {}

fn main() -> Result<(), Box<std::error::Error>> {
    let matches = App::new("pig data process")
        .version(env!("CARGO_PKG_VERSION"))
        .author(env!("CARGO_PKG_AUTHORS"))
        .arg(
            Arg::with_name("dir")
                .long("dir")
                .short("d")
                .help("dir of data")
                .value_name("DIR")
                .takes_value(true),
        )
        .get_matches();

    let decorator = slog_term::TermDecorator::new().build();
    let drain = slog_term::FullFormat::new(decorator).build().fuse();
    let drain = slog_async::Async::new(drain).build().fuse();
    let log = slog::Logger::root(drain, o!());

    let dir = matches.value_of("dir").expect("need --dir parameter");
    info!(log, "dir = {}", dir);

    let mut files = file::read_dir(dir);
    files.sort_by(|a, b| {
        a.file_name()
            .into_string()
            .unwrap()
            .cmp(&b.file_name().into_string().unwrap())
    });
    info!(log, "{:?}", files);
    let mut cnt = 0;
    for file in &files {
        let fd = fs::File::open(file.path()).unwrap();
        info!(log, "read {}", file.file_name().into_string().unwrap());
        let mut buffer = BufReader::new(fd);
        let mut is = CodedInputStream::new(&mut buffer);
        while let Ok(message) = is.read_message::<Message>(){
//            info!(log, "{:?}", message);
            cnt+=1;
        }
    }
    info!(log, "total = {}", cnt);
    Ok(())
} 
```