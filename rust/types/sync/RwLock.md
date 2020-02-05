# RwLock

`RefCell`のmulti thread版。borrow system同様に、複数のreaderか、単一のwriterの存在を許容する。  
`RefCell`と違い、`RwLock`はincompatibleなborrowでpanicしない。(blockする)
複数のreaderの存在を許容できない場合は、`Mutex`を検討する。

| `RefCell`    | `RwLock`  |
| ---------    | --------- |
| `borrow`     | `read`    |
| `borrow_mut` | `write`   |


## Node example

```rust
use std::sync::{Arc, RwLock};
use std::thread;

#[derive(Debug)]
pub struct Node<T>(NodeRef<T>);

type NodeRef<T> = Arc<RwLock<NodeInner<T>>>;

#[derive(Debug)]
struct NodeInner<T> {
    value: T,
    adjacent: Vec<NodeRef<T>>,
}

impl<T> Node<T> {
    pub fn new(value: T) -> Self {
        let n = NodeInner {
            value,
            adjacent: Vec::new(),
        };
        Node(Arc::new(RwLock::new(n)))
    }
    pub fn add_adjacent(&self, adjacent: &Node<T>) {
        self.0
            .write()
            .expect("write lock")
            .adjacent
            .push(Arc::clone(&adjacent.0));
    }
}

pub struct Graph<T> {
    nodes: Vec<Node<T>>,
}

impl<T> Graph<T> {
    pub fn from_nodes(nodes: Vec<Node<T>>) -> Self {
        Self { nodes }
    }
}

fn main() {
    let n1 = Node::new(1);
    let n2 = Node::new(2);
    let n3 = Node::new(3);

    n1.add_adjacent(&n2);
    n1.add_adjacent(&n3);
    n2.add_adjacent(&n3);

    let graph = Arc::new(Graph::from_nodes(vec![n1,n2,n3]));

    let view_handler = {
        let graph = Arc::clone(&graph);
        let msg = "failed to get read lock";
        thread::spawn(move || {
            for _ in 0..10 {
                for node in &graph.nodes {
                    let node = node.0.read().expect(&msg);
                    let value = node.value;
                    let neighbors = node.adjacent.iter().map(|n|
                        n.read().expect(&msg).value
                    ).collect::<Vec<_>>();
                   println!("node {} is connected to: {:?}", value, neighbors);
                }

                println!("--------------------");
                thread::yield_now();
            }
        })
    };

    let update_handler = {
        let graph = Arc::clone(&graph);
        let msg = "failed to get write lock";
        thread::spawn( move || {
            for _ in 0..10 {
                for node in &graph.nodes {
                    let mut node = node.0.write().expect(msg);
                    node.value += 10;
                }
                thread::yield_now();
            }
        })
    };

    view_handler.join().expect("thread join");
    update_handler.join().expect("thread join");
}
```