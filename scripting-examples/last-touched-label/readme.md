# Last Touched Label
THis project implements a label which is updated with the name of the control most recently touched. It uses a script attached to each of the controls on a screen to update the label.

## Script
Each control in the project must have this script attached:

``` Lua
local ltl = self.parent:findByName("last_touched")

function onValueChanged(key)
  if ("touch" == key) then
    ltl.values.text = self.name
  end
end
```

When a control it touched, it updates the `text` property of `last_touched`, the label (or other control with a `text` property) which displays the name of the last-touched control.

`last_touched` could be swapped for any control which has a `text` field, as it only serves to display the control name. If the control is changed or renamed, the script attached to each control would need to be updated with the new storage control's name.

## Potential Improvements
It would be great to find a way to do this without requiring a script attached to every button. This might be possible through use of a frame or grid.

## Local Messages
A second version of the project (`last-touched-label-local.tosc`) is also included to demonstrate the same behavior using local messages instead of scripts.