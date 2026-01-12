📐 Measures (DAX Metrics Used)
1️⃣ Revenue

Description: Total revenue realized from all bookings

Revenue = SUM(fact_bookings[revenue_realized])


Table: fact_bookings

2️⃣ Total Bookings

Description: Total number of bookings made

Total Bookings = COUNT(fact_bookings[booking_id])


Table: fact_bookings

3️⃣ Total Capacity

Description: Total room capacity available across hotels

Total Capacity = SUM(fact_aggregated_bookings[capacity])


Table: fact_aggregated_bookings

4️⃣ Total Successful Bookings

Description: Total successful bookings across all hotels

Total Successful Bookings = 
SUM(fact_aggregated_bookings[successful_bookings])


Table: fact_aggregated_bookings

5️⃣ Occupancy %

Description: Percentage of rooms occupied out of total available capacity

Occupancy % = 
DIVIDE([Total Successful Bookings], [Total Capacity], 0)


Table: fact_aggregated_bookings

6️⃣ Average Rating

Description: Average customer rating across bookings

Average Rating = AVERAGE(fact_bookings[ratings_given])


Table: fact_bookings

7️⃣ Number of Days

Description: Total number of days present in the dataset

No of days = 
DATEDIFF(
    MIN(dim_date[date]),
    MAX(dim_date[date]),
    DAY
) + 1


Table: dim_date

8️⃣ Total Cancelled Bookings

Description: Total bookings with status Cancelled

Total Cancelled Bookings =
CALCULATE(
    [Total Bookings],
    fact_bookings[booking_status] = "Cancelled"
)


Table: fact_bookings

9️⃣ Cancellation %

Description: Percentage of cancelled bookings over total bookings

Cancellation % =
DIVIDE([Total Cancelled Bookings], [Total Bookings])


Table: fact_bookings

🔟 Total Checked Out

Description: Total bookings successfully checked out

Total Checked Out =
CALCULATE(
    [Total Bookings],
    fact_bookings[booking_status] = "Checked Out"
)


Table: fact_bookings

1️⃣1️⃣ Total No Show Bookings

Description: Bookings where customers neither cancelled nor checked in

Total No Show Bookings =
CALCULATE(
    [Total Bookings],
    fact_bookings[booking_status] = "No Show"
)


Table: fact_bookings

1️⃣2️⃣ No Show Rate %

Description: Percentage of no-show bookings

No Show Rate % =
DIVIDE([Total No Show Bookings], [Total Bookings])


Table: fact_bookings

1️⃣3️⃣ Booking % by Platform

Description: Percentage contribution of each booking platform

Booking % by Platform =
DIVIDE(
    [Total Bookings],
    CALCULATE(
        [Total Bookings],
        ALL(fact_bookings[booking_platform])
    )
) * 100


Table: fact_bookings

1️⃣4️⃣ Booking % by Room Class

Description: Percentage contribution of each room class

Booking % by Room Class =
DIVIDE(
    [Total Bookings],
    CALCULATE(
        [Total Bookings],
        ALL(dim_rooms[room_class])
    )
) * 100


Tables: fact_bookings, dim_rooms

1️⃣5️⃣ ADR (Average Daily Rate)

Description: Average revenue earned per booked room

ADR = DIVIDE([Revenue], [Total Bookings], 0)


Table: fact_bookings

1️⃣6️⃣ Realisation %

Description: Percentage of bookings successfully realized

Realisation % =
1 - ([Cancellation %] + [No Show Rate %])


Table: fact_bookings

1️⃣7️⃣ RevPAR (Revenue Per Available Room)

Description: Revenue generated per available room

RevPAR = DIVIDE([Revenue], [Total Capacity])


Tables: fact_bookings, fact_aggregated_bookings

1️⃣8️⃣ DBRN (Daily Booked Room Nights)

Description: Average number of rooms booked per day

DBRN = DIVIDE([Total Bookings], [No of days])


Tables: fact_bookings, dim_date

1️⃣9️⃣ DSRN (Daily Sellable Room Nights)

Description: Average number of rooms available for sale per day

DSRN = DIVIDE([Total Capacity], [No of days])


Tables: fact_aggregated_bookings, dim_date

2️⃣0️⃣ DURN (Daily Utilized Room Nights)

Description: Average number of rooms utilized per day

DURN = DIVIDE([Total Checked Out], [No of days])


Tables: fact_bookings, dim_date

📈 Week-over-Week (WoW) Change Measures

Common logic: compares current week with previous week using dim_date[wn]

2️⃣1️⃣ Revenue WoW Change %
Revenue WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR revCW = CALCULATE([Revenue], dim_date[wn] = selW)
VAR revPW =
    CALCULATE(
        [Revenue],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(revCW, revPW, 0) - 1

2️⃣2️⃣ Occupancy WoW Change %
Occupancy WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR occCW = CALCULATE([Occupancy %], dim_date[wn] = selW)
VAR occPW =
    CALCULATE(
        [Occupancy %],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(occCW, occPW, 0) - 1

2️⃣3️⃣ ADR WoW Change %
ADR WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR adrCW = CALCULATE([ADR], dim_date[wn] = selW)
VAR adrPW =
    CALCULATE(
        [ADR],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(adrCW, adrPW, 0) - 1

2️⃣4️⃣ RevPAR WoW Change %
RevPAR WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR revparCW = CALCULATE([RevPAR], dim_date[wn] = selW)
VAR revparPW =
    CALCULATE(
        [RevPAR],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(revparCW, revparPW, 0) - 1

2️⃣5️⃣ Realisation WoW Change %
Realisation WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR realCW = CALCULATE([Realisation %], dim_date[wn] = selW)
VAR realPW =
    CALCULATE(
        [Realisation %],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(realCW, realPW, 0) - 1

2️⃣6️⃣ DSRN WoW Change %
DSRN WoW Change % =
VAR selW =
    IF(
        HASONEFILTER(dim_date[wn]),
        SELECTEDVALUE(dim_date[wn]),
        MAX(dim_date[wn])
    )
VAR dsrnCW = CALCULATE([DSRN], dim_date[wn] = selW)
VAR dsrnPW =
    CALCULATE(
        [DSRN],
        FILTER(ALL(dim_date), dim_date[wn] = selW - 1)
    )
RETURN
DIVIDE(dsrnCW, dsrnPW, 0) - 1
