
---
title: "Examples"
linkTitle: "Examples"
weight: 3
date: 2017-01-05
description: >
  See some examples of using DDS!
---


## Client
```rust
mod sdk_1;
use sdk_1::Dds;
use serde::{Deserialize, Serialize};
use std::env;

#[derive(Serialize, Deserialize, Debug)]
pub struct GreetingsParam {
    message: String,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let args = env::args().skip(1).collect::<Vec<_>>();
    let addr = &args[0];

    let dds = Dds::new("http://".to_string() + addr);
    let task_id = dds
        .run(
            "greetings".to_string(),
            bincode::serialize(&GreetingsParam {
                message: "hello".to_string(),
            })
            .unwrap(),
            vec![
                ("127.0.1.1:50051".to_string(), "initiator".to_string()),
                ("127.0.1.2:50051".to_string(), "receiver".to_string()),
                ("127.0.1.3:50051".to_string(), "receiver".to_string()),
            ],
        )
        .await?;
    println!("ID={}", task_id);

    Ok(())
}
```

