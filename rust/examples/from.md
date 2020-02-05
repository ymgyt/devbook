# From


## SortedVec

```rust
/// Our simple sorted vector structure is just a wrapper around a Vec
struct SortedVec<T>(Vec<T>);

/// Converting slices into SortedVec is pretty much expected.
impl<'a, T: Ord + Clone> From<&'a [T]> for SortedVec<T> {
    fn from(slice: &[T]) -> Self {
        let mut vec = slice.to_owned();
        vec.sort();
        SortedVec(vec)
    }
}

/// Converting a Vec is also expected.
/// We can sort the vector in place and then put it inside SortedVec.
impl<T: Ord + Clone> From<Vec<T>> for SortedVec<T> {
    fn from(mut vec: Vec<T>) -> Self {
        vec.sort();
        SortedVec(vec)
    }
}

/// Converting a LinkedList also makes sense, but it has no
/// slice representation, so we'll have to rely on its iterator.
impl<T: Ord + Clone> From<LinkedList<T>> for SortedVec<T> {
    fn from(list: LinkedList<T>) -> Self {
        let mut vec: Vec<T> = list.iter().cloned().collect();
        vec.sort();
        SortedVec(vec)
    }
}

let vec = vec![1u8, 2, 3];
// Convert a slice
let sorted = SortedVec::from(&vec[1..]);
// ... a vector
let sorted = SortedVec::from(vec);
// ... a linked list
let mut linked_list: LinkedList<u8> = LinkedList::new();
linked_list.extend(&[1, 2, 3]);
let sorted = SortedVec::from(linked_list);
```

https://ricardomartins.cc/2016/08/03/convenient_and_idiomatic_conversions_in_rust


## PacketType

```rust
/// Represents a packet type.
/// Associated with each variant is its raw numeric representation.
enum PacketType {
    Data  = 0, // packet carries a data payload
    Fin   = 1, // signals the end of a connection
    State = 2, // signals acknowledgment of a packet
    Reset = 3, // forcibly terminates a connection
    Syn   = 4, // initiates a new connection with a peer
}

impl From<PacketType> for u8 {
    fn from(original: PacketType) -> u8 {
        match original {
            PacketType::Data  => 0,
            PacketType::Fin   => 1,
            PacketType::State => 2,
            PacketType::Reset => 3,
            PacketType::Syn   => 4,
        }
    }
}

impl TryFrom<u8> for PacketType {
    type Err = ParseError;
    fn try_from(original: u8) -> Result<Self, Self::Err> {
        match original {
            0 => Ok(PacketType::Data),
            1 => Ok(PacketType::Fin),
            2 => Ok(PacketType::State),
            3 => Ok(PacketType::Reset),
            4 => Ok(PacketType::Syn),
            n => Err(ParseError::InvalidPacketType(n))
        }
    }
}
```