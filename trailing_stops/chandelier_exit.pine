//@version=6
// Copyright (c) 2019-present, Alex Orekhov (everget)
// Chandelier Exit script may be freely distributed under the terms of the GPL-3.0 license.
indicator('Chandelier Exit', shorttitle = 'CE', overlay = true)

const string calcGroup = 'Calculation'
length = input.int(22, title = 'ATR Period', group = calcGroup)
mult = input.float(3.0, step = 0.1, title = 'ATR Multiplier', group = calcGroup)
useClose = input.bool(true, title = 'Use Close Price for Extremums', group = calcGroup)

const string visualGroup = 'Visuals'
showLabels = input.bool(true, title = 'Show Buy/Sell Labels', group = visualGroup)
highlightState = input.bool(true, title = 'Highlight State', group = visualGroup)

const string alertGroup = 'Alerts'
awaitBarConfirmation = input.bool(true, title = 'Await Bar Confirmation', group = alertGroup)

//---

atr = mult * ta.atr(length)

longStop = (useClose ? ta.highest(close, length) : ta.highest(length)) - atr
longStopPrev = nz(longStop[1], longStop)
longStop := close[1] > longStopPrev ? math.max(longStop, longStopPrev) : longStop

shortStop = (useClose ? ta.lowest(close, length) : ta.lowest(length)) + atr
shortStopPrev = nz(shortStop[1], shortStop)
shortStop := close[1] < shortStopPrev ? math.min(shortStop, shortStopPrev) : shortStop

var int dir = 1
dir := close > shortStopPrev ? 1 : close < longStopPrev ? -1 : dir

const color textColor = color.white
const color longColor = color.green
const color shortColor = color.red
const color longFillColor = color.new(color.green, 85)
const color shortFillColor = color.new(color.red, 85)

buySignal = dir == 1 and dir[1] == -1
longStopPlot = plot(dir == 1 ? longStop : na, title = 'Long Stop', style = plot.style_linebr, linewidth = 2, color = longColor)
plotshape(buySignal ? longStop : na, title = 'Long Stop Start', location = location.absolute, style = shape.circle, size = size.tiny, color = longColor)
plotshape(buySignal and showLabels ? longStop : na, title = 'Buy Label', text = 'Buy', location = location.absolute, style = shape.labelup, size = size.tiny, color = longColor, textcolor = textColor)

sellSignal = dir == -1 and dir[1] == 1
shortStopPlot = plot(dir == 1 ? na : shortStop, title = 'Short Stop', style = plot.style_linebr, linewidth = 2, color = shortColor)
plotshape(sellSignal ? shortStop : na, title = 'Short Stop Start', location = location.absolute, style = shape.circle, size = size.tiny, color = shortColor)
plotshape(sellSignal and showLabels ? shortStop : na, title = 'Sell Label', text = 'Sell', location = location.absolute, style = shape.labeldown, size = size.tiny, color = shortColor, textcolor = textColor)

midPricePlot = plot(ohlc4, title = '', display = display.none, editable = false)

fill(midPricePlot, longStopPlot, title = 'Long State Filling', color = (highlightState and dir == 1 ? longFillColor : na))
fill(midPricePlot, shortStopPlot, title = 'Short State Filling', color = (highlightState and dir == -1 ? shortFillColor : na))

await = awaitBarConfirmation ? barstate.isconfirmed : true
alertcondition(dir != dir[1] and await, title = 'CE Direction Change', message = 'Chandelier Exit has changed direction, {{exchange}}:{{ticker}}')
alertcondition(buySignal and await, title = 'CE Buy', message = 'Chandelier Exit Buy, {{exchange}}:{{ticker}}')
alertcondition(sellSignal and await, title = 'CE Sell', message = 'Chandelier Exit Sell, {{exchange}}:{{ticker}}')
