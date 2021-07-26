# Destructor

Bulk object destructor for simplifying cleanup tasks. Supports functions, instances, and connections.

## API

#### `Destructor.new()`

Creates a new object.

#### `Destructor:Add(item)`

Adds an item to be finalized on the next call to `Destroy`.
Throws an error if the object's type is unsupported.

#### `Destructor:Destroy()`

Finalizes items that have been added and removes all items from the Destructor.
The finalizers are defined for various types as follows:
- `Function`: Calls the function.
- `Instance`: Calls :Destroy() on the object.
- `RBXScriptConnection`: Calls :Disconnect() on the object.
- `table`: Calls :Destroy() on the table, assuming that it is an object.

## Example

```lua
-- Creation:
local dtor = Destructor.new()


-- Functions:
dtor:Add(function()
    print("Foo")
end)
dtor:Destroy() -- > Foo


-- Instances:
local newPart = Instance.new("Part", workspace)
dtor:Add(newPart)
wait(1)
dtor:Destroy() -- newPart is :Destroy()ed


-- Connections:
dtor:Add(RunService.Stepped:Connect(function()
    print("Bar") -- Starts printing "Bar" every frame
end))
wait(1)
dtor:Destroy() -- Stops printing "Bar" every frame

-- Objects:
local class = {}
class.__index = class

function class.new()
    return setmetatable({), class)
end

function class:Destroy()
    -- clean up (typically with another Destructor)
end

local object = class.new()
dtor:Add(object)
dtor:Destroy() -- object is :Destroy()ed
```
