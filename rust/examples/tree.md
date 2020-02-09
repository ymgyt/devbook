# Tree

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
pub struct Tree(Rc<RefCell<TreeInner>>);

#[derive(Debug, Clone)]
pub struct TreeNode(Weak<RefCell<TreeInner>>);

#[derive(Debug)]
struct TreeInner {
    children: Vec<Tree>,
    parent: Option<TreeNode>,
}

impl Tree {
    pub fn new() -> Self {
        Self(Rc::new(RefCell::new(TreeInner{
            children: Vec::new(),
            parent: None,
        })))
    }

    pub fn to_node(&self) -> TreeNode {
        TreeNode(Rc::downgrade(&self.0))
    }
}

impl TreeNode {
    pub fn parent(&self) -> Option<Self> {
        let rc = self.0.upgrade().expect("This tree is already disposed");
        let locked = rc.borrow();
        locked.parent.clone()
    }

    pub fn children(&self) -> Vec<Self> {
        let rc = self.0.upgrade().expect("This tree is already exposed");
        let locked = rc.borrow();
        locked.children.iter().map(|child| Self(Rc::downgrade(&child.0))).collect()
    }

    pub fn push(&self, child: Tree) {
        let rc = self.0.upgrade().expect("This tree is already disposed");
        {
            let mut child_locked = child.0.borrow_mut();
            assert!(child_locked.parent.is_none(), "Cannot have multiple parents");
            child_locked.parent = Some(self.clone())
        }
        let mut locked = rc.borrow_mut();
        locked.children.push(child);
    }

    pub fn pop(&self) -> Option<Tree> {
        let rc = self.0.upgrade().expect("This tree is already disposed");
        let mut locked = rc.borrow_mut();

        let child = locked.children.pop()?;
        {
            let mut child_locked = child.0.borrow_mut();
            child_locked.parent = None;
        }
        Some(child)
    }
}
```