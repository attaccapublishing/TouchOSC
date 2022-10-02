# Fader Percent Display
This project demonstrates a few different ways to display a fader's value using scripts.

## Scripts
Each of the display controls implements a slightly different script:

### Raw value
Faders use a floating-point value between 0 and 1 as their `x` value. This script will display the raw floating-point value:

``` Lua
function update()
  local fader = self.parent:findByName("fader1")
  self.values.text = fader.values.x
end
```

This will display a value like `0.3537937`.

### Percentage
The raw fader value can be converted into a percentage using Lua's string formatting functionality. Below are examples of rounding to the nearest percent (i.e., `35%`) and the nearest tenth of a percent (i.e., `35.4%`):

``` Lua
function update()
  local fader = self.parent:findByName("fader1")
  self.values.text = string.format("%i%%", (fader.values.x * 100))
end
```

``` Lua
function update()
  local fader = self.parent:findByName("fader1")
  self.values.text = string.format("%.1f%%", (fader.values.x * 100))
end
```

## String Formatting
There are a number of ways to achieve the same string formatting. For example, the script which rounds to the nearest percent could also be written as:

``` Lua
function update()
  local fader = self.parent:findByName("fader1")
  self.values.text = math.floor(fader.values.x * 100) .. "%"
end
```

String formatting using `string.format` is helpful in the case of finer-grained displays such as the tenth-of-a-percent display, as it allows for explicit selection of things like number format and number of decimal places.

More information on string formatting in Lua can be found [here](https://www.lua.org/manual/5.1/manual.html#5.4). Lua uses C-style `printf` formatting, described [here](https://cplusplus.com/reference/cstdio/printf/).