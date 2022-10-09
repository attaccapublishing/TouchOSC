# BPM Blink
This project implements two different versions of blinking a control at a given BPM calulated by tapping a button. It is based on [F-l-i-x](https://github.com/F-l-i-x)' [bpm_tap](https://github.com/F-l-i-x/TouchOSC/tree/main/examples/bpm_tap) example project.

One button builds on the `bpm_tap` example to blink the BPM display label at the calculated BPM. The other blinks itself using the same logic, just without the second control for displaying the BPM value.

## Scripts
### Separate BPM Display
In this case, the button is used to calculate the tapped BPM. This script comes directly from `bpl_tap_01.tosc` with a minor modification to round the BPM value to an integer.
#### Button
```Lua
local last = 0
local ring = {0, 0, 0, 0, 0}
local i = 1

function onValueChanged(key)
  if key == 'touch' and self.values.touch == true then
  
    ring[i%5+1] = (getMillis() - last)
    last = getMillis()
    
    local avg = 0
    
    for j=1,#ring do
      avg = avg + ring[j]
    end
 
    self.parent.children.label2.values.text = math.floor((60000 / (avg / #ring)) + 0.5)
    i = i + 1
  end
end
```

This script relies on the name of the label - in this case `label` - to update the control with the calculated BPM.

#### Label
The label implements the blink behavior by setting its color value:

```Lua
local blink_time_ms = 100
local blink_color = Color(1, 1)
local off_color = Color(0.35, 1)
local next_blink_start = -1
local blink_end_ms = -1
local ms_per_beat = 0

function onValueChanged(key)
  if key == "text" then
    -- Convert BPM to milliseconds per beat
    ms_per_beat = 60000 / tonumber(self.values.text)
    -- Set the blink active and queue the next blink start 
    self.color = blink_color
    blink_end_ms = getMillis() + blink_time_ms
    next_blink_start = getMillis() + ms_per_beat
  end
end

function update()

  if (-1 == next_blink_start) then
    -- Set the blink rate based on the current text value
    ms_per_beat = 60000 / tonumber(self.values.text)
    next_blink_start = getMillis() + ms_per_beat
  else
    if (getMillis() >= next_blink_start) then
      self.color = blink_color
      blink_end_ms = getMillis() + blink_time_ms
      next_blink_start = getMillis() + ms_per_beat
    end
  end
  
  if ((-1 ~= blink_end_ms) and (getMillis() >= blink_end_ms)) then
    self.color = off_color
    blink_end_ms = -1
  end
end
```

The `onValueChanged` function is used to update the internal milliseconds-per-beat value and to trigger the next blink. `update` checks the current time against the next start or end of a blink to determine the color it should display.

One key value in this script is `blink_time_ms`: this controls how long the control stays in the active color for each blink. In this case, it is set to 100ms.

### Single Button
The single-button script looks remarkably similar to the others, as it effectively combines both pieces of the BPM display into a single control:

```Lua
local last = 0
local ring = {0, 0, 0, 0, 0}
local i = 1
local blink_time_ms = 100
local blink_color = Color(1, 1)
local off_color = Color(0.35, 1)
local next_blink_start = -1
local blink_end_ms = -1
local ms_per_beat = 100

function onValueChanged(key)
  if key == 'touch' and self.values.touch == true then
  
    ring[i%5+1] = (getMillis() - last)
    last = getMillis()
    
    local avg = 0
    
    for j=1,#ring do
      avg = avg + ring[j]
    end
 
    ms_per_beat = (avg / 5)
    print(ms_per_beat)
    i = i + 1
    
    -- Set the blink active and queue the next blink start 
    self.color = blink_color
    blink_end_ms = getMillis() + blink_time_ms
    next_blink_start = getMillis() + ms_per_beat
  end
end

function update()
  if (-1 == next_blink_start) then
    -- Set the blink rate based on the current text value
    self.color = blink_color
    blink_end_ms = getMillis() + blink_time_ms
    next_blink_start = getMillis() + ms_per_beat
  else
    if (getMillis() >= next_blink_start) then
      self.color = blink_color
      blink_end_ms = getMillis() + blink_time_ms
      next_blink_start = getMillis() + ms_per_beat
    end
  end
  
  if ((-1 ~= blink_end_ms) and (getMillis() >= blink_end_ms)) then
    self.color = off_color
    blink_end_ms = -1
  end
end
```

Note that this version skips the BPM calculation and display because the blink triggers off of milliseconds per beat, not beats per minute.

## Timing Accuracy
The `update` function is only called when TouchOSC re-draws the screen, which appears to be locked to the display's refresh rate. For example, on a 60Hz display, `update` is called once every ~16.67ms. This means that the BPM blinking will not be exact and may drift slightly. A display with a higher refresh rate can improve accuracy, but ultimately some innaccuracy should be expected.