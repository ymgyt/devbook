# graph

```rust
use std::cell::RefCell;
use std::rc::Rc;

// A graph can be represented in several ways. For the sake of illustrating how
// interior mutability works in practice, let's go with the simplest
// representation: a list of nodes.
// Each node has an inner value and a list of adjacent nodes it is connected to
// (through a directed edge).
// That list of adjacent nodes cannot be the exclusive owner of those nodes, or
// else each node would have at most one edge to another node and the graph
// couldn't also own these nodes.
// We need to wrap Node with a reference-counted box, such as Rc or Arc. We'll
// go with Rc, because this is a toy example.
// However, Rc<T> and Arc<T> enforce memory safety by only giving out shared
// (i.e., immutable) references to the wrapped object, and we need mutability to
// be able to connect nodes together.
// The solution for this problem is wrapping Node in either Cell or RefCell, to
// restore mutability. We're going to use RefCell because Node<T> doesn't
// implement Copy (we don't want to have independent copies of nodes!).

// Represents a reference to a node.
// This makes the code less repetitive to write and easier to read.
type NodeRef<T> = Rc<RefCell<Node<T>>>;

struct Node<T> {
    inner_value: T,
    adjacent: Vec<NodeRef<T>>,
}

impl<T> Node<T> {
    // Creates a new node with no edges.
    fn new(inner: T) -> Node<T> {
        Node { inner_value: inner, adjacent: vec![] }
    }

    // Adds a directed edge from this node to other node.
    fn add_adjacent(&mut self, other: NodeRef<T>) {
        self.adjacent.push(other);
    }
}

struct Graph<T> {
    nodes: Vec<NodeRef<T>>,
}

fn main() {
    // Create some nodes
    let node_1: NodeRef<u32> = Rc::new(RefCell::new(Node::new(1)));
    let node_2: NodeRef<u32> = Rc::new(RefCell::new(Node::new(2)));
    let node_3: NodeRef<u32> = Rc::new(RefCell::new(Node::new(3)));

    // Connect some of the nodes (with directed edges)
    (node_1.borrow_mut()).add_adjacent(node_2.clone());
    (node_1.borrow_mut()).add_adjacent(node_3.clone());
    (node_2.borrow_mut()).add_adjacent(node_1.clone());
    (node_3.borrow_mut()).add_adjacent(node_1.clone());

    // Add nodes to graph
    let mut graph = Graph { nodes: vec![] };
    graph.nodes.push(node_1);
    graph.nodes.push(node_2);
    graph.nodes.push(node_3);

    // Show every node in the graph and list their neighbors
    for node in graph.nodes.iter().map(|n| n.borrow()) {
        let value = node.inner_value;
        let neighbours = node.adjacent.iter()
            .map(|n| n.borrow().inner_value)
            .collect::<Vec<_>>();
        println!("node ({}) is connected to: {:?}", value, neighbours);
    }
}
```

https://play.rust-lang.org/?gist=bd01037fd9b9bb3d1a15bde61f580c6f


```rust
use std::sync::{Arc, Mutex, Weak};

#[derive(Debug, Clone)]
pub struct Graph(Arc<Mutex<Vec<Arc<Mutex<NodeInner>>>>>);

impl Graph {
    pub fn new() -> Self {
        Self(Arc::new(Mutex::new(Vec::new())))
    }

    pub fn new_node(&self) -> Node {
        let node = Arc::new(Mutex::new(NodeInner {
            neighbors: Vec::new(),
        }));
        let node_weak = Node(Arc::downgrade(&node));
        let mut lock = self.0.lock().unwrap();
        lock.push(node);
        node_weak
    }
}

#[derive(Debug, Clone)]
pub struct Node(Weak<Mutex<NodeInner>>);

impl Node {
    pub fn push(&self, neighbor: Node) {
        let rc = self.0.upgrade().expect("The graph is dead");
        let mut lock = rc.lock().unwrap();
        lock.neighbors.push(neighbor);
    }
}

#[derive(Debug)]
struct NodeInner {
    neighbors: Vec<Node>,
}
```

https://qiita.com/qnighy/items/4bbbb20e71cf4ae527b9