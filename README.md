How am I supposed to iterate over and mutate over the list of entities when it depends on the list of entities

Is the problem that the hashmap owns the entities?
I want to iterate twice and mutate





Hi can someone please help me improve my architecture to get rid of the aliasing? So naively I just want to do this:
```rust
struct GameState {
    entities: HashMap<u32, Entity>,
}

impl GameState {
    // mutates the entity by moving as far as is appropriate
    // mutates self by writing collision events to a buffer
    fn slide(&mut self, entity_id: u32, dx: f32, dy: f32) {
        ...
    }

    fn update(&mut self) {
        ...
        for (entity_key, entity) in self.entities.iter() {
            if entity.obeys_gravity {
                self.slide(*entity_key, 0.0, self.gravity);
            }
        }
        ...
    }
}
```
Its not allowed because in the line `for (entity_key, entity) in self.entities.iter() {` self is borrowed, and then when `slide` is called thats trying to mutably borrow it as well.

Below is an adjusted design that also doesnt work:
```rust
struct GameState {
    entities: HashMap<u32, Entity>,
}

impl GameState {
    // returns new bounding box and list of collisions
    fn slide(&self, entity_id: u32, dx: f32, dy: f32) -> (Rect, Vec<CollisionEvent>) {
        ...
    }

    fn update(&mut self) {
        ...
        for (entity_key, entity) in self.entities.iter_mut() {
            if entity.obeys_gravity {
                let (bounding_box, collisions) = self.slide(*entity_key, 0.0, self.gravity);
                entity.bounding_box = bounding_box;
                
            }
        }
        ...
    }
}
```
Because you cant borrow self inside that iter_mut I guess


------------------------


OK on to the next thing
actually properly implement the apply movement thing
implement left and right
implement jumping