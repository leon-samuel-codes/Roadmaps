# Plan — Linked List / Stack / Queue from Scratch

**Script:** `linked_list_stack_queue.py`
**Prerequisite:** at least a basic classes intro (`class`, `__init__`, `self`,
methods) — not the full OOP block. This is the OOP warm-up, so those concepts
click before refactoring FRIDAY into classes.

**Concepts:** functions, conditionals, loops.
**New:** classes, `self`, instance attributes.

**Deps:** none — pure standard library.

## Definition of done

You can add, find, remove, and print a linked list; push/pop a stack; and
enqueue/dequeue a queue — each backed by its own class with working methods.
`reverse()` is optional polish but genuinely worth doing.

## Phases

### Phase 1 — The `Node` class
```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
```
- The whole building block — everything else is nodes pointing to other nodes.

### Phase 2 — `LinkedList`: add
- `LinkedList.__init__` starts with `self.head = None` (track `self.tail` too to skip the walk).
- `add(value)` creates a new `Node` and links it to the end of the chain:
  walk from `head` following `.next` until a node whose `.next is None`, then
  point that node's `.next` at the new one — or use `self.tail` for O(1).
- Handle the empty-list case (`head is None`) explicitly.

### Phase 3 — `LinkedList`: find and remove
- `find(value)` walks from `head`, comparing each node's value; returns the node or `None`.
- `remove(value)` walks the chain keeping track of the **previous** node so it
  can re-point `prev.next` around the node being removed — the trickiest part
  conceptually, take it slow.
- Special cases: removing the head (update `head`), removing the tail (update
  `tail`), value not found (no-op).

### Phase 4 — Print the whole list
- `__str__` (or a `display()` method) walks from `head` and builds a readable
  string like `1 -&gt; 2 -&gt; 3 -&gt; None` — lets you see the contents while testing.

### Phase 5 — `Stack` (push/pop)
- Plain Python list internally: `self.items = []`.
- `push(value)` → `self.items.append(value)`; `pop()` → `self.items.pop()`.
- The point isn't reinventing storage — it's practicing the push/pop
  *interface* as its own class.
- Decide and document empty-pop behavior (raise or return `None`).

### Phase 6 — `Queue` (enqueue/dequeue)
- `enqueue(value)` adds at one end, `dequeue()` removes from the other.
- Use `collections.deque` (efficient version); a plain `list.pop(0)` works but
  is O(n) — note that in a comment.

### Phase 7 — Test everything
- `if __name__ == "__main__":` script:
  - build a `LinkedList`, add a few values, remove one, print the result
  - same for `Stack` and `Queue` (confirm FIFO order for the queue)
- Confirms each class works before moving on.

### Phase 8 — Extension: `reverse()`
- Walk the chain once, flipping each node's `.next` to point backward, then
  update `head` to be the old tail (and `tail` to be the old head).
- One of the most commonly asked interview questions on linked lists — worth
  the extra rep.

## Verify

The `__main__` block runs clean, including edge cases:
- add/find/remove on a normal list
- remove a missing value (no crash)
- remove the head node
- `reverse()` on a 1-element list and a multi-element list (check `head`/`tail`)
- `pop()` on an empty stack (matches documented behavior)
- `dequeue()` returns items in FIFO order