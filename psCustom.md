# Customizations

## Rendering events in basic week and basic day

### Desired structure

1. Separated by working area (empty row in between)
2. Shifts in one row (same level)
3. Earlier start time higher
4. If same start time, events with earlier end time higher

#### Example
- Shift 1: every day 07:00 - 08:00
- Shift 2: every day 08:00 - 09:00
- Event 1: Tuesday, 07:30 - 10:30
- Event 2: Tuesday, 08:00 - 10:00
- Event 3: Wednesday, 10:00 - 11:00

<table>
  <thead>
    <tr>
      <th>Mon</th>
      <th>Tue</th>
      <th>Wed</th>
      <th>...</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>07:00 - 08:00<br>Shift 1</td>
      <td>07:00 - 08:00<br>Shift 1</td>
      <td>07:00 - 08:00<br>Shift 1</td>
      <td>07:00 - 08:00<br>Shift 1</td>
    </tr>
    <tr>
      <td></td>
      <td>07:30 - 10:30<br>Event 1</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>08:00 - 09:00<br>Shift 2</td>
      <td>08:00 - 09:00<br>Shift 2</td>
      <td>08:00 - 09:00<br>Shift 2</td>
      <td>08:00 - 09:00<br>Shift 2</td>
    </tr>
    <tr>
      <td></td>
      <td>08:00 - 10:00<br>Event 2</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>10:00 - 11:00<br>Event 3</td>
      <td></td>
    </tr>
  </tbody>
</table>

### Code research

#### 19.01.2015
`eventsToSegs()` in Grid.event.js gets a list of events as input.
The items of the `events` variable are the ones we get from the backend.
`eventsToSegs()` first calls `eventsToRanges()` which wraps every event into *ranges* which is comprise of items like this:

    {
      event: the_original_event_object,
      end: Moment,
      start: Moment,
      eventDurationMS: 3600000,
      eventStartMS: 1422198000000
    }

Then `eventRangeToSeg()` is called on each of these items. It transforms each item of *ranges* into *segs*, which look like this:

    {
      // not sure what this is
      isEnd: true,
      isStart: true,
      // specifies the leftmost column of this event, probably only relevant for events overlapping multiple days
      leftCol: 2,
      rightCol: 2,
      // already known
      eventDurationMS: 3600000,
      eventStartMS: 1422198000000,
      event: the_original_event_object,
      // specifies the row where the event is placed in the grid
      level: 6
    }

The `level` property however is *NOT* assigned in the `eventRangeToSeg()` function, what I suspected at first.
This property though is critical in order to customize the event placement to our needs.

Three different functions use the `level` property:

- `generateSegPositionCSS()` in TimeGrid.events.js
- `buildSlotSegLevels()` in TimeGrid.events.js
- `buildSegLevels()` in DayGrid.events.js <== This one will be relevant for us in order to customize the basicWeek/Day view.

Call hierarchy:

- `renderSegRow()` in DayGrid.events.js calls
- `buildSegLevels()` in DayGrid.events.js

`renderSegRow()` renders a row for each level, so by modifying how the levels are built (via `buildSegLevels()`)
we should be able to get the event rendering we would like :-)

#### Earlier



