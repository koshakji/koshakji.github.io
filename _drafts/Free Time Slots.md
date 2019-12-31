# Free Time Slots

In university, I like to time block commitments, lectures and study time on my calendar. It helps me understand what I should be doing at any given moment, which is especially easy while using the big [Fantastical](https://apps.apple.com/us/app/fantastical-2-for-iphone/id718043190) complication on the Inforgaph Modular face on my Apple Watch.
The only problem is ease of capture. Even with Fantastical's incredible natural language engine, which can parse almost anything I type into an actual event with the right details, I still have to decide on when I'm doing something at the time of capturing it, and that's not ideal given I might be adding a bunch of things at the same time. This is why I tend to add assignments to my task manager of choice (Reminders) first, and block out the necessary time on my calendar later. I found the process of reviewing my Reminders lists, estimating times to accomplish certain tasks, and going back and forth to find free times on my calendar tiresome, especially when trying to schedule something involving other people, which is a lot of my university projects and assignments.

So, I set out to automate some of this process. What I came up with is a couple of Shortcuts that work together to help me find free time and schedule my tasks into calendar events. The bigger, more complex Shortcut is one I think I'll use in more Shortcuts as a function that, given a starting date, a time block duration and a maximum number of blocks to check, returns all of the possible free blocks of this duration on my calendar starting at the specified date and time.

The interesting part of this Shortcut is figuring out which time blocks a certain calendar event conflicts with. It sounds easy: if the starting time or the ending time of an event is within a block of time, then it's busy, that was my first thought. However that doesn't take into account events which span multiple time blocks. If an event starts at 1 PM and ends at 4 PM the Shortcut should figure out that the 1-2 PM, 2-3 PM and 3-4 PM blocks are busy for example, even though the 2-3 PM block doesn't contain the start or end date of the event. Another complication comes from the way the Shortcuts' `if [date] is between [date 1] and [date 2]` action works: it's inclusive on both sides, which means that if an event starts at a specific time, and a possible time block ends at that time, this comparison would result in the block looking busy, even though it isn't, because the start time of the event is “Between” the start and end times of the block, according to Shortcuts. The same happens when the ending time of an event is the same as the starting time of a possible time block.

What I did was: I calculated the starting time for every possible time block, then, for every events starting a day earlier than the start date (that way I would get most events that started earlier but didn’t end until after the starting date, which means they conflict with some of the earlier time blocks), and loop over each time block.
The trick I used to getting rid of the event starting at the end of time block situation, is I compare the times of the event with the block times, adjusted by one minute. So I see if the events starts between the start of the time block and the end minus one minute, and if the end of the event happens is between the start of the block plus one minute, and the end of the block.

Once I figure out an event does conflict with one block, I try to figure out which other blocks does it conflict with. To do that, I calculate the duration of the event and divide it by the duration of my time block. That would give me the maximum number of possibly conflicting time blocks, with this specific event. I use that number to loop over the time blocks (until I reach that number, or the start/end of my search period) and mark these time blocks for deletion, with one exception: if the event’s start date conflicts with a time block, I check if its end date conflicts with the last time block before marking it for deletion, and a similar thing happens when the end date of an events is conflicting with the time block, the first time block could be free. That happens when the event’s duration happen to be dividable by the duration of each time block, which means it would be starting at the end of a time block, or ending at the start of one.