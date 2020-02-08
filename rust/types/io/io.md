# io

## stdout

```rust
use std::io::{self,Write};

let stdout = io::stdout();
let stdout = stdout.lock(); 
let mut handle = io::BufWriter::new(stdout);
writeln!(handle, "{}", 10);
```