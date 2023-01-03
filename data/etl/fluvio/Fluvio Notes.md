### Environments
- [infinyon cloud](https://infinyon.cloud/account)
- [fluvio docs](https://www.fluvio.io/docs/tutorials/cloud-setup/)
- [rancher docs](https://docs.rancherdesktop.io/1.6/how-to-guides/hello-world-example)

### Commands
- create topic
```shell
fluvio topic create greetings
```
- publish to a topic
```shell
echo "Hello, Fluvio" | fluvio produce greetings
```
- consume a topic
```shell
fluvio consume greetings -B -d
```
- login to cloud
```shell
fluvio cloud login --use-oauth2
```
- check profiles
```shell
fluvio profile list
fluvio profile current
```
- switch profile
```shell
fluvio profile switch <profile name>
```

`fluvio consume <topic> [flags]` by default prints new records to the terminal as they enter the database. By default it runs nonstop until quit – the examples used here all use the `-d` flag to tell it to stop.

Some useful option flags to be aware of:

-   `-d` – halts consumption after reaching the end of the records available.
-   `-T [int]` – consumes only the specified most recent records (default 10).
-   `-B [int]` – starts consuming records a specified offset after the start of the database (default 0).
-   `-p <int>` – reads only from the specified partition.
-   `-k` – displays the key portion of the key and value pairs.
-   `-A` – reads from all partitions available.
-   `--smartmodule <module>` – runs a [SmartModule](https://fluvio.io/smartmodules/) module on the output, then displays the results.

### SmartModule Projects
- generate new SmartModule project
```shell
smdk generate regex_filter
```
- add any necessary deps to Cargo.toml
```shell
...
once_cell = "1.13.0"
regex = "1.6.0"
...
```
- add the code to the module in lib.rs
```rust
// lib.rs
use once_cell::sync::OnceCell;
use regex::Regex;

use fluvio_smartmodule::{
   dataplane::smartmodule::{SmartModuleExtraParams, SmartModuleInitError},
   eyre, smartmodule, Record, Result,
};

static REGEX: OnceCell<Regex> = OnceCell::new();

#[smartmodule(init)]
fn init(params: SmartModuleExtraParams) -> Result<()> {
   if let Some(regex) = params.get("regex") {
       REGEX
           .set(Regex::new(regex)?)
           .map_err(|err| eyre!("regex init: {:#?}", err))
   } else {
       Err(SmartModuleInitError::MissingParam("regex".to_string()).into())
   }
}

#[smartmodule(filter)]
pub fn filter(record: &Record) -> Result<bool> {
   let string = std::str::from_utf8(record.value.as_ref())?;
   Ok(REGEX.get().unwrap().is_match(string))
}
```
- build it
```shell
smdk build
```
- test it
```shell
smdk test -e regex="^[0-9]*$" --text 42
```
- load it
```shell
smdk load
```
- verify it (list the module as being loaded in the cluster)
```shell
fluvio sm list
```
- run it (prep a file to test the data filter functionality)
```shell
echo "
42
abc
abc123
" > values.txt
```
- create a topic to produce this text
```shell
fluvio produce filter-test -f values.txt
```
- consume a topic with the module
```shell
fluvio consume filter-test -dB --smartmodule my-group/regex-filter@0.1.0 -e regex="^[0-9]*$"
```
- shorten the module name if it's unique
```shell
fluvio consume filter-test -dB --smartmodule regex-filter -e regex="^[0-9]*$"
```
