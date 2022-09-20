# Outline Last-Touched Control
This project implements a series of scripts which outlines only the control which was touched most recently. This is implemented by using an invisible Label object to track the active control and to update the affected controls.

## Script
Each control in the project must have this script attached:

``` Lua
local ltv = self.parent:findByName("last_touched_var")

function onValueChanged(key)
  if ("touch" == key) then
    ltv.values.text = self.name
  end
end
```

The hidden label control named `last_touched_var` is assigned this script:
```Lua
local latch = ""

function onValueChanged(key)
  if (latch ~= self.values.text) then
    local new_last = self.parent:findByName(self.values.text)
    local old_last = self.parent:findByName(latch)
    
    print("new: " .. new_last.name)
    new_last.outline = true
    if (nil ~= old_last) then
      print("old: " .. old_last.name)
      old_last.outline = false
    end
    latch = self.values.text
  end
end
```

When a control is touched, it updates the `text` property of `last_touched_var` with that control's name. This then triggers `last_touched_var`'s `onValueChanged` routine to draw an outline on the triggering control and to remove the outline from the latched control. The triggering control is then latched, ready for the next change.

`last_touched_var` could be swapped for any control which has a `text` field, as it only serves as a place to store a string. If the control is changed or renamed, the script attached to each control would need to be updated with the new storage control's name.

## Potential Improvements
It would be great to find a way to do this without requiring a script attached to every button. This might be possible through use of a frame or grid. It might also be better to have each control enable its own outline and have `last_touched_var` be responsible only for latching and hiding the latched control's outline.