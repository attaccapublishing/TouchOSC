# Exclusive Grid Row Selector
This project implements a grid of buttons in which each row can only have a single button latched at any given time. Pressing any other button in that row will un-latch any button that might be latched.

## Script
The button grid has the following script assigned to implement the row-exclusive behavior:

``` Lua
function onValueChanged(key)
  if (self.type == ControlType.BUTTON) then
    if (key == "x" and self.values.x == 1) then
      -- Grids are 1-indexed
      local self_idx = self.index - 1
      local rel_row_idx = (self_idx % self.parent.gridX) + 1
      local row_start_idx = (self_idx - rel_row_idx) + 2
      for i = row_start_idx,(row_start_idx + self.parent.gridX - 1),1
      do
        if (i ~= self.index) then
            self.parent.children[i].values.x = 0
        end
      end
    end
  end
end
```

This script relies on grids having reliable numbering: left-to-right, top-to-bottom starting with an index of 1 at the top left of the grid . It will scale to any size grid object as it uses the grid's `gridX` property to determine the size of each row. 

There's a bit of nudging values into the right place (all of the `+1`s) to account for the grid's index starting at 1. 