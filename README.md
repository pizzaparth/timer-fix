# Fixing the bugs:
## Problems I noticed in the Original code.
- Timer speeds up on clicking "Start"
Clicking "Start" doesn't check if the timer is already running. So each click starts a new, separate countdown running at the same time as the old one(s). With the timer running twice, the number drops by 2 every second instead of 1.

- Timer goes negative
The code never checks if the time has reached 0. It just keeps subtracting 1 every second forever, so once it hits 0 it keeps going into negative numbers instead of stopping.

- Below 10 seconds
The code doesn't add a "0" in front of single-digit seconds. So instead of showing 9:09, 9:08, etc., it shows 9:9, 9:8, the leading zero is just missing, making it look a bit off. And once the timer goes negative and it shows weird stuff like -1:-1.

## Causes for the bugs accorind to me:
### 1. Timer speeds up on clicking "Start"

- startTimer() calls setInterval(...) every time it runs, but never saves the interval ID it gets back.
- There's no check for "is a timer already running?" before starting a new one.
- The "Start" button is never disabled after the first click, so it can be clicked repeatedly.
- Result: every click adds another independent interval, all counting down the same timeLeft at once.

### 2. Timer goes negative

- The interval callback does timeLeft-- unconditionally, with no if (timeLeft <= 0) check.
- There's no clearInterval() call anywhere in the file, so nothing ever stops the interval once it's started.
- Because of bug #1, there may even be multiple intervals running, so there'd be nothing to stop any of them anyway.

### 3. Missing leading zero below 10 seconds

- seconds is calculated with timeLeft % 60, which gives a plain number (e.g. 9, not "09").
- That number is placed directly into the text (minutes + ":" + seconds) with no formatting step to pad single digits with a leading zero.
- Combined with bug #2, once timeLeft goes negative, JavaScript's % and Math.floor behave oddly with negative numbers, making the missing-zero problem look even stranger (e.g. -1:-1).

## Fixing the bugs:
### The timer speeds up on clicking "start":
- Added a timerId variable to keep track of whether an interval is currently running.
- Before starting a new interval, startTimer() now checks if timerId is already set — if it is, the function just returns and does nothing.
- This means clicking "Start" multiple times no longer creates multiple overlapping intervals, so timeLeft only ever gets decremented once per second, no matter how many times the button is clicked.

### Timer goes negative:
- Inside the interval callback, added a check if (timeLeft <= 0) at the very start.
- When that condition is true, it calls clearInterval(timerId) to stop the interval from firing again, then returns immediately without decrementing further.
- This means once the timer reaches 0, it stops there instead of continuing into negative numbers.

### Missing leading zero below 10 seconds:
- Introduced secondsText which checks if seconds < 10, and if so, builds the string "0" + seconds instead of using the raw number.
- Otherwise it just uses seconds as-is (for values 10–59, no padding is needed).
- The display line now uses secondsText instead of seconds directly, so the timer always shows two digits for the seconds part (e.g. 9:09 instead of 9:9).