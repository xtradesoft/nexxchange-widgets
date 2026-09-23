# Nexxchange Widgets

* [TeeTime Widget](#nexxchange-teetime-widget) 
* [Tournament Widget](#tournament-widget) 
* [Events Widget](#events-widget) 

# Nexxchange TeeTime Widget
TeeTime widget to book within the Nexxchange Marketplace <https://www.nexxchange.com>

## Overview
The example HTML code <https://github.com/xtradesoft/nexxchange-widgets/blob/master/example/index.html> shows exactly how to implement the **Nexxchange** tee time widget. 

It is only a simple copy and paste.

`Pull Requests` are welcome.

### Widget parameters

* **issuer**

	Used to specify the golf club for which to display tee times.
	The value should be set to the golf clubs issuer ID (see [How to find the issuer ID?](#how-to-find-the-correct-issuer-id))

* **showBookingButton**

	(default: true) Optional, allows to en/disable the booking button
	
* **onlyShowAvailable**

	(default: false) Optional, allows to show only available tee time
	
* **course**

    Optional, integer value, server only sends tee times for the selected golf course.
	
* **i18n**

	Optional, defines the used translation in the widget.

	* booking: "Buchen"
	* available: "verfügbar"
	* noGreenfees: 'Keine Startzeiten für den ausgewählten Zeitraum verfügbar.'

* **render**

  A custom render function. Uses returned server data to render tee times into any DOM element.
  
  While this method gives the highest degree of flexibility is also exposes internal data representations that might change in the future.
  The return data currently uses the following schema:
  
  ```
  {
    issuerId?:string
    golfCourse: {
      courseName:string
      teeTimes:TeeTime[]  // array of tee times
    }
  }
  ```

  `TeeTime` currently uses the following schema:

  ```
  {
    courseName:string
    teeTimes:TeeTimeSlot[] // array of tee time slots
  }
  ```  

  `TeeTimeSlot` currently uses the following schema:

  ```
  {
    tt:number
    ttTime:string
    ttDate:DateTime
    countAvail:number facet:Seq[TeeTimeSlotFacet]
  }
  ```

  Finally `TeeTimeSlotFacet` currently uses this schema:
  
  ```
  {
    facetName:String
    facetId:Long
    price?: { amount: number, currency: { code:string, symbol:string  } }
  }
  ```

* **date** 	(dd.mm.yyyy)
* **hour**
* **minutes**

### Helpers

The widget has one public function "reload" which accepts the following parameters

The widget also provides the ```createBookingButtonLink(issuerId:string,teeTimeSlot:TeeTimeSlot,facet:Facet)``` methoed to render the booking button link.

### Styling
Size, color, etc. can be adjusted individually using standard CSS

### Examples

<img src="https://github.com/xtradesoft/nexxchange-widgets/blob/master/example/img/Example-Image-using-widget.png?raw=true" alt="Website Screenshot" width="800">

<img src="https://github.com/xtradesoft/nexxchange-widgets/blob/master/example/img/Example-Image-using-widget-2.png?raw=true" alt="Website Screenshot" width="800">

<img src="https://github.com/xtradesoft/nexxchange-widgets/blob/master/example/img/Example-Image-using-widget-3.png?raw=true" alt="Website Screenshot" width="800">

### Selection of Live Implementations

[GC Drautal] (https://www.drautalgolf.at/tee-time)

[Mehr Grün] (https://www.mehr-gruen.at/buchung/)

# Tournament Widget

The tournament widget allows integration of a tournament table on external website.

* Minimal configuration: Include javascript and invoke `renderTournamentWidget` to load the tournament widget into a DOM element.
* Self contained: CSS is non-intrusively (namespaced) injected by the tournament widget. Note that most of the injected CSS is need by [react-datepicker](https://github.com/Hacker0x01/react-datepicker).
* Responsive by default: Uses some basic (namespaced) [Bootstrap 4](https://getbootstrap.com/) CSS styles to properly flow on mobile devices.
* No iframes required.
* Configurable: See [widget parameter section](#widget-parameter-section).

## Integration

Load the tournament widget Javascript from https://portal.nexxchange.com to import the `renderTournamentWidget` function into the global namespace. 
Invoke `renderTournamentWidget` for a given DOM element ID and pass the desired `issuerId` in the [parameter section](#widget-parameter-section).

```
<div id="tournament-widget-container"></div>
...
<script src="https://portal.nexxchange.com/assets/widgets/js/tournament-widget.js"></script>
<script>
  renderTournamentWidget(
    "tournament-widget-container",   // First argument sets the ID of the target DOM element  
    {  // Second argument is used for configuration parameters
      issuerId : "<issuerId>"  // set correct issuer ID for your golf club here
    }
  );
</script>
```

A full example page can be found here: https://b2b.nexxchange.com/playground/tournament-widget.html

<img src="https://github.com/xtradesoft/nexxchange-widgets/blob/master/example/img/tournament-widget.png?raw=true" alt="Tournament Widget in Action" width="800">

## Widget Parameter Section

| Name         | Type        | Description           |
| -------------|-------------|-----------------------|
| issuerId     | string \| [string] | Issuer IDs of the displayed golfclubs (see [How to find the issuer ID?](#how-to-find-the-correct-issuer-id)) |
| fromDate     | Date        | Lower bound for tounament date, the upper bound is fixed to `fromDate` + 1 year |
| lang         | string      | Language used to render tournament widget: `en`, `da`, `de`, `es`, `fr`, `it`, `no`, `sv`. Date picker will show names of month and weekdays in the selected language. |
| dateFormat   | string      | Date format used to show the selected date, e.g "dd.MM.yyyy eee" see [date-fns](https://date-fns.org/v2.28.0/docs/format) for all formatting options |
| spinner      | string      | `donat` or `donat multi` or `ripple` |
| hideFromDatePicker | boolean | If set to `true` the date picker will be hidden, you can use the [Widget API](#widget-api) to update the from date programatically. |
| maxResults   | integer     | maximum number of displayed tournaments, maxResults is capped to 10 entries. |
| i18nDictionary | Map<string,string> | Overwrite translation keys for selected language, see the [I18n](#i18n) section. |

## Widget API

Methods can be called on the widget instance returned by `renderTournamentWidget`.

### Update From Date (`setFromDate`)

Example:
```
<div id="tournament-widget-container"></div>
...
<script src="https://portal.nexxchange.com/assets/widgets/js/tournament-widget.js"></script>
<script>
  var widget = renderTournamentWidget(
    "tournament-widget-container",
    { issuerId : "<issuerId>" }
  );
  const date = new Date(2019, 1, 1)
  widget.setFromDate(date);
</script>
```

A change in the `fromDate` will automatically trigger a reload.

### I18n

The `i18n` property can be used to override the default translations provided by the widget or to define translations for unsupported languages.
The following translation keys are available: 

`registrationDeadline`,`remainingTickets`,`availableTickets`,`registerButtonLabel`,`round`

### Custom Styling

Most of the elements in the widget have proper class names assigned to them to allow customization of the CSS styles.
E.g. by default the tournament date is left aligned while the name/type of the tournament is right aligned.
To move the date to the right side and the title to the left side you can use the following custom CSS:
```
.tournament-date {
  float: right !important;
}
.tournament-name {
  float: left !important;
}
.tournament-type {
  float: left !important;
}
```

### Debugging

In case of load errors (reload icon is shown) please check the browser console for more details.

## Tournament Widget FAQ

### How to find the correct issuer ID?

1. Go to https://www.nexxchange.com
2. Select an golf club/issuer using the search field
3. Inspect the page source and look for the meta tag with the `name` attribute set to `issuerId`. The `content` attribute of this meta tag contains the issuer ID. 

Example:
```
...
<meta name="issuerId" content="112233445566778899001122">`
...
```
### Can I use my own date picker instead of the one that ships with the widget?

Yes, you can use the `setFromDate` method described in the [Widget API](#widget-api) section to control the from date used by the widget.
Also use the `hideFromDatePicker` property to hide the built-in date picker.

### What happens in case the widget cannot load the tournament data for some reason?

The spinner will be replaced by a reload icon. Clicking on the icon will trigger a reload request.

# Events Widget

The events widget shows the upcoming events of a golf club on an external website.

* Minimal configuration: include one script and call `renderEventsWidget` for a DOM element.
* Self contained: plain JavaScript. Its stylesheet is injected by the widget and can be overridden with plain CSS.
* No iframes required.
* Data only: the widget can hand you the raw JSON instead of rendering it, see [Data only](#data-only).

## Integration

```
<div id="events-widget"></div>
...
<script src="https://www.nexxchange.com/assets/widgets/js/events-widget.js"></script>
<script>
  renderEventsWidget(
    "events-widget",            // ID of the target DOM element
    { issuerId: "<issuerId>" }  // issuer ID of your golf club, see "How to find the correct issuer ID?"
  );
</script>
```

Example pages: [example/events.html](example/events.html) shows the widget's default look,
[example/events-customized.html](example/events-customized.html) shows it with some customization.

Each event is rendered as one row: title (linking to the event page on the marketplace) followed by the event type
(a small divider in the type's colour and the type name), start and end date and time, the availability ("x spots left",
"almost fully booked", waiting list or fully booked) and a "Book Now" button leading to the booking on the marketplace. 

The widget shows the events of one day (today by default, or the chosen `fromDate`), at most 10 events.

## Widget Parameter Section

| Name              | Type     | Description                                                                                                                                                                          |
| ----------------- | -------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| issuerId          | string   | Issuer ID of the golf club (required)                                                                                                                                                |
| lang              | string   | Language of the labels and of the weekday and month names, default `en`                                                                                                              |
| fromDate          | Date     | The day whose events are shown, default today                                                                                                                                        |
| eventType         | integer  | Show only events of this type. To find the id: open the events page of your club on the marketplace, choose the type in the filter, and read the `eventType` value from the page URL |
| availableOnly     | boolean  | Show only events with free spots                                                                                                                                                     |
| showBookingButton | boolean  | Hide the "Book Now" button when `false`                                                                                                                                              |
| showDatePicker    | boolean  | Show a date picker above the events to choose the day. Default `false`                                                                                                               |
| i18n              | Map<string,string> | Overrides single labels: `bookNow`, `noResults`, `date`, `spotsLeft`, `almostFullyBooked`, `fullyBooked`, `waitingList` (`{0}` is replaced by the number of spots)                   |
| render            | function | Data only: your own rendering, see below                                                                                                                                             |

`renderEventsWidget` returns a widget object with one method, `reload(options)`, which re-fetches events with the changed
options, e.g. `widget.reload({ availableOnly: true })`.

## Data only

Pass a `render` function to receive the JSON instead of the built-in view. The function gets the data and the container element:

```
renderEventsWidget("events-widget", {
  issuerId: "<issuerId>",
  render: function (data, container) {
    // see the schema below
  }
});
```

The response:

```
{
  config: {
    i18nDictionary: { bookNow, noResults, date, spotsLeft, almostFullyBooked, fullyBooked, waitingList }  // labels in the requested language
  },
  events: [
    {
      title: string,
      startDateTime: string,          // ISO-8601 in the club's local time, e.g. "2026-09-21T06:00:00+02:00"
      endDateTime?: string,
      eventType?: { name: string, color?: string },
      remainingRegistrations?: number, // absent when the event has no maximum
      almostFull: boolean,
      canBeRegistered: boolean,
      canJoinWaitingList: boolean,
      eventDetailsLink: string,        // event detail page on the marketplace
      bookingLink: string              // booking on the marketplace;
    }
  ]
}
```

## Styling

The widget injects its stylesheet at the top of `<head>`, so the stylesheet of your page overrides it with plain
selectors, no `!important` needed. All elements carry a class inside `.nx-events-widget`: `nx-event-row`, `nx-event-title-row`
with `nx-event-title`, `nx-event-type-divider` and `nx-event-type`, `nx-event-meta`, `nx-event-date`, `nx-event-availability`
(with `nx-state-warning` or `nx-state-danger`), `nx-event-book` and, with the date picker, `nx-event-datepicker` with
`nx-event-datepicker-label` and `nx-event-datepicker-input`.
Colours have custom properties on the widget root:

```
.nx-events-widget {
  --nx-book-color: #1e4234;   /* "Book Now" button */
  --nx-row-odd-bg: #eef3f8;   /* background of odd rows */
  --nx-row-even-bg: #fff;     /* background of even rows */
}
.nx-events-widget .nx-event-title { color: #333; }
```

The font is inherited from your page. 

### Debugging

In case of load errors (reload icon is shown), please check the browser console for more details.

### License

Apache
