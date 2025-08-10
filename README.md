https://fanciful-sable-e3f7e4.netlify.app/

Context

We service portable toilets on a routine basis.

The most common frequencies are:

Weekly
Fortnightly (every 2 weeks) (default)
Every 4 weeks
None

Custom schedules (e.g. Mon/Wed/Fri, specific time windows)

Some customers also have time-based constraints (e.g. only accessible between 6.30am–7.30am), or coordination requirements (e.g. servicing must align with a third-party technician at a set time).

Task
Create a ServiceFrequency component in Vue 3 using Shadcn-vue that meets the following requirements:

Renders as a clean, bordered card using Shadcn’s Card component with a label

Displays the following frequency options as a vertical bullet or radio list:

Weekly

Fortnightly (default selected)

Every 4 weeks

None

Custom

Custom Behaviour
When the “Custom” option is selected:

Reveal a previously hidden section within the card

Display:

Checkboxes for Monday through Sunday, allowing multiple days to be selected. Unchecking should update the prop (see below).

A checkbox labeled “Specify time window(s)”

When checked, allow input of a start and end time or collection of times (e.g. 06:30–07:30,15:00-17:00)

When unchecked again, should clear these values from the JSON string (see below)

A checkbox labeled “Set exact time”

When checked, allow input of a single specific time (e.g. 13:30)

When unchecked again should clear value.

Keep the interface clean and usable—layout, spacing, and clarity matter.


JSON Input and Output Handling
The component should:

Accept a scheduleConfig prop (passed from the controller as a JSON string)

If the string is empty or null, default to Fortnightly

Internally manage the selected frequency and any custom options

Display the full updated JSON string in a clearly separate section of the screen, so I can see what would be sent back to the controller for persistence

You do not need to implement the actual emit/update logic, but the structure of the JSON should reflect your understanding of how this data would be stored and used in downstream scheduling logic.

What to Submit
Record a short video (maximum 60 seconds) that shows:

What the component does
How long it took to build
Your location and timezone
Your expected hourly rate in AUD
You do not need to send your code. The video is enough.

A note on AI use: You're welcome to use AI tools (like GitHub Copilot or ChatGPT) to assist with the code — I do too.

What matters to me is that you understand what the component does, why it’s structured the way it is, how it fits into a real-world system, and that you've added your own, appropriate flair to the UI/UX.

If it’s entirely AI-generated and you don’t understand it, that will be obvious in the video.