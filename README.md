# Tableau for Archive of Our Own Exchange Data

Ever wish it were easier to hand-match [AO3](https://archiveofourown.org/) exchange participants or track due dates, extensions, and pinch hits? You can use Tableau!

**This guide discusses how to shape exchange data for Tableau and includes a cleaned single-fandom dummy data set you can play with.** You can view, interact with, and download a [sample Tableau workbook using this data set hosted on Tableau Public](https://public.tableau.com/profile/ladyofthelog#!/vizhome/AO3ExchangeMatchingandAssignmentTracking/Overview). I include screenshots of this workbook below.

This guide does not cover the operational aspects of exchange moderation or how to split, pivot, and otherwise manipulate data. I generally use Tableau Prep to shape data I'm analyzing in Tableau, but you can use Python, R, and many other tools.

## Safe Data Handling

When an AO3 user signs up to participate in an exchange you moderate, the email associated with their AO3 account becomes visible to anyone modding and is included in the raw signup data that you can download from AO3. Some of these emails can contain personally identifying information like legal names and educational institutions. **Be cautious when handling signup data from AO3.** If you store or display exchange signup data in a tool used for collaboration like Google Sheets or Tableau, make sure you only share it with trusted fellow moderators.

## Formatting Conventions

For ease of reading, I've formatted text in the following way:

* Bold: **Tableau Dashboards and Dashboard Panes.** 
* Quotes: "Columns"
* Italics: *Cell Values*

## Tables

This guide is not going to cover the finer points of exchange configuration, just what you'll need to prepare for matching. I personally prefer to run the AO3 matching algorithm to find all of the people who can only match to each other, then hand match beyond that. 

I use two tables for every exchange dashboard:
* **Assignments.** Contains one row for every exchange assignment. Recipient pseud functions as the primary key for all data.  (Live Google Sheet.)
* **Signups.** Many rows. A single record (here, a row in a table) represents a possible match. (Can be live or static file.)

## Shaping Exchange Signup Data

Raw signup AO3 data has one row for every signup.

| Pseud | Email | Request 1 | Description | Offer 1 | Description |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | 
| Asko | asko@asko.com | Doctor Who, Twelfth Doctor/The Doctor's TARDIS | Lorem Ipsum | Doctor Who, Twelfth Doctor/The Doctor's TARDIS | Lorem Ipsum |

For matching, you'll want to be able to look at what you're matching *on*, not what individual fields in a signup a participant filled out. Requests and Offer columns are split and pivoted; in their place, a new column, "Type," is created with two possible values: *Offer* and *Request.* In the **Matching Dashboard,** I used this field to create the **Request** and **Offer** matching panes where you can filter on "Fandom" and "Pairing" in both panes at the same time.

My dummy data set assumes that any recipient and creator must match on one ship in one fandom. Accordingly, this is how two sample records look.

| Type | Pseud | Relationship | Fandom | Description | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| Request | Asko | Twelfth Doctor/The Doctor's TARDIS | Doctor Who | Lorem Ipsum |
| Offer | Dtae | Clara Oswin Oswald/Amy Pond | Doctor Who | Lorem Ipsum |

For a single-fandom exchange matching on pairing and rating...

| Type | Pseud | Relationship | Maximum Rating | Description | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| Request | Asko | Twelfth Doctor/The Doctor's TARDIS | General Audiences | Lorem Ipsum |
| Request | Asko | Twelfth Doctor/The Doctor's TARDIS | Teen & Up | Lorem Ipsum |

For a multifandom exchange matching on fandom and character...

| Type | Pseud | Character | Fandom | Description | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| Request | Asko | Twelfth Doctor | Doctor Who | Lorem Ipsum |
| Offer | Asko | The Doctor's TARDIS | Doctor Who | Lorem Ipsum |

For a single-fandom, single-pairing exchange matching on archive warnings and additional tags...

| Type | Pseud | Archive Warnings | Additional Tags | Description | 
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| Offer | Asko | No Archive Warnings Apply | Alternate Universe - College/University | Lorem Ipsum |
| Offer | Asko | Major Character Death | Alternate Universe - College/University | Lorem Ipsum |

## Creating Your Assignments Sheet

Now that you've shaped data from signups, it's smooth sailing from here! I use Google Sheets to host my Assignments sheet, which allows me to easily update my dashboards with live data. Since I work with another moderator on most of my exchanges, we can work on matching and assignments together or asynchronously. Here's what your assignment sheet should look like.

| Due Date | Status | Recipient | Creator | Match Quality | Recip Email | Pinch Hitter | Notes |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | 
| 01-20-21 | Complete | Asko | Vljx | ok | asko@asko.com | None | None |

Let's go over the columns here: 
* **Due Date.** When a work is due. Updated if extensions are given.
Status. Current state of assignment. Possible values: *Open / Open - Extension / Open - Pinch Hit / Complete.*
* **Recipient.** 
* **Creator.** Creator originally assigned to Recipient. If the creator drop out/default, this value will not change, but status will be changed to *Open - Pinch Hit* and a pinch hitter, once assigned, will go into the "Pinch Hitter" column.
* **Match Quality.** Helpful during matching while you're shuffling people around to know which matches you want to keep if possible and which you're open to breaking.
* **Recip Email.** You may want to remove this field and exclusively access this information from the archive's UI to be extra safe, but it can be convenient to have in one place.
* **Pinch Hitter.** If a pinch hitter is assigned, their username will go here.
* **Notes.** Any notes you may have.


## Matching Dashboard

Now that your data is clean and shaped, you can plug it into Tableau and play. [View the sample matching dashboard on Tableau Public.](https://public.tableau.com/profile/ladyofthelog#!/vizhome/AO3ExchangeMatchingandAssignmentTracking/Matching)

![Screenshot of Matching Dashboard using dummy data](/image1.png)

For multifandom exchanges, I recommend setting Relationship/Character filters to show *Only Relevant Values* instead of *All Values.* That way, if you select Fandoms first, you will only see Relationships/Characters for selected fandoms. 

## Assignment Tracking Dashboard

[View the sample assignment tracking dashboard on Tableau Public.](https://public.tableau.com/profile/ladyofthelog#!/vizhome/AO3ExchangeMatchingandAssignmentTracking/AssignmentTracking)

![Screenshot of Assignment Tracking Dashboard using dummy data](/image2.png)

This dashboard pulls entirely from your Assignments sheet to help you get a big picture view of outstanding assignments. I use a calculated field to check whether or not an assignment is overdue:

```
IF MIN(TODAY(), [Due Date]) = [Due Date] THEN TRUE

ELSE FALSE

END
```

# Happy Matching!
