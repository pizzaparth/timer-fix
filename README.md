## Problems I noticed in the Original code.
- Timer speeds up on clicking "Start"
Clicking "Start" doesn't check if the timer is already running. So each click starts a new, separate countdown running at the same time as the old one(s). With the timer running twice, the number drops by 2 every second instead of 1.

- Timer goes negative
The code never checks if the time has reached 0. It just keeps subtracting 1 every second forever, so once it hits 0 it keeps going into negative numbers instead of stopping.

- Below 10 seconds
The code doesn't add a "0" in front of single-digit seconds. So instead of showing 9:09, 9:08, etc., it shows 9:9, 9:8, the leading zero is just missing, making it look a bit off. And once the timer goes negative and it shows weird stuff like -1:-1.