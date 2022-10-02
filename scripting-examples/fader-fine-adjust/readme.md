# Fader Fine-Adjust
This project implements a pair of buttons which use scripts to provide a way to adjust a fader using a finite value rather than relying on potentially-imprecise touch controls.

## Script
Each of the find-adjust buttons implements the same script, just with a different `increment` value:

``` Lua
local fader_1 = self.parent:findByName("fader_1")
local increment = 0.05

function onValueChanged(key)
  if (key == "x" and self.values.x == 1) then
    fader_1.values.x =  fader_1.values.x + increment
  end
end
```

The increment value can be changed to any value (though be aware that [floating-point](https://en.wikipedia.org/wiki/Floating-point_arithmetic) limitations may come into play). Positive `increment` values will increase the fader's level, negative values will decrease it. Any number of buttons could be added with the same script and different increment values.

Note that if the fader control's name (`fader_1`) was changed, any controls with this script would have to be updated.