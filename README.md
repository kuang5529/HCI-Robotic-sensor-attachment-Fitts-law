# Robotic Sensor Attachment — Fitts’ Law Experiment

A single-file, browser-based human–computer interaction experiment. Use a mouse to drag a sensor attached to a simulated robotic arm onto a circular attachment zone on an equipment surface.

The application collects **120 successful trials**, calculates results for **20 distance/width conditions**, and generates a scatter plot with a fitted Shannon Fitts’ Law equation and R².

## Scenario

A maintenance operator remotely controls a robotic arm through a top-view camera interface to place a sensor at a specified attachment location on an equipment surface. The operator must position the sensor accurately while keeping the task efficient.

## Innovation

The experiment translates a conventional pointing task into sensor-attachment positioning. The circular target represents the acceptable region for the sensor center, so target width (W) has a concrete meaning: positioning tolerance. A two-link arm with fixed link lengths makes the end-effector motion visible. Multidirectional movements are arranged in opposite-angle pairs, with three rightward and three leftward trials for every distance/width condition.

## Application and design rationale

This design was chosen because remote sensor installation presents a practical speed–accuracy tradeoff: a smaller allowable placement region demands finer corrections, while a more distant location requires more movement. The real-world HCI problem is how to design a remote positioning interface that supports accurate sensor placement without unnecessarily increasing operator effort and completion time.

The measured relationship can inform discussions about target presentation, positioning tolerances, and expected task time in equipment-maintenance interfaces. The results describe this mouse-controlled simulation; they do not directly predict a physical robot’s performance, which also depends on dynamics, camera calibration, latency, and safety constraints.

## Custom formula and empirical results

The complete Shannon Fitts’ Law model is:

```text
MT = a + b × log2(A / W + 1)
```

The participant-reported empirical model from a completed session is:

```text
MT = 297.47 + 133.55 × log2(A / W + 1) (ms)
R² = 0.8745
```

- **a = 297.47 ms:** the fitted intercept, representing the model’s estimated baseline time at ID = 0. Because the tested conditions have positive ID, this is an extrapolation rather than a directly measured zero-difficulty trial. It should not be interpreted as a pure reaction-time measurement.
- **b = 133.55 ms/bit:** each additional bit of difficulty corresponds to an estimated 133.55 ms increase in movement time.
- **R² = 0.8745:** the linear model accounts for approximately 87.45% of the variation in the condition-mean movement times used for that fit.

These coefficients were supplied by the participant; the underlying session CSV is not included in this repository. New sessions calculate their own equation and R² from their 20 condition means, without overwriting this documented reference result. Software verification runs are not participant measurements.

## Files and requirements

- [`index.html`](index.html): the complete application, including styles, experiment logic, chart generation, and exports.
- `README.md`: this user guide.

Use a desktop browser with JavaScript enabled and a physical mouse. No installation, external libraries, build process, account, or server is required to run the downloaded HTML file. Open `index.html` directly in your browser, or open the URL where the file is hosted. The same file can be served by GitHub Pages.

Maximize the browser before starting. The workspace must be at least **682 CSS pixels wide**, at least **320 CSS pixels high**, and fully visible. These are workspace dimensions, not the dimensions of the entire browser window. Keep the window size, browser zoom, and mouse settings unchanged during a session.

## Interface

The layout uses two columns in a **2:1 ratio**:

- **Left:** the robotic workspace, current trial, distance (A), target width (W), difficulty (ID), progress, and status messages.
- **Right:** Scenario, Instructions, and Results. This column scrolls independently while the workspace stays in place.

The robot base is centered on the lower edge of the equipment surface. Link lengths are set for the current layout and remain fixed while dragging; the joints rotate to follow the sensor. This is a position-control simulation, without physical robot dynamics, collision constraints, or communication delay.

## Run an experiment

1. Open the application and ensure the whole workspace is visible. If screen recording is required, start recording before the experiment.
2. Click **Start experiment**.
3. Find the blue sensor and press and hold the **left mouse button** on it. This starts the trial timer.
4. Drag the sensor toward the circular attachment zone.
5. Release the button when the **sensor center** is inside or on the target boundary. The whole sensor does not need to fit inside the circle.
6. If the release misses, grab the sensor again and correct its position. The timer continues and the missed release is counted.
7. After a successful release, the next trial appears automatically. Locate the sensor at its new starting position and repeat. There is no Next Trial button.
8. Complete all **120 successful trials**. The results chart opens automatically.

Moving the pointer back to the next starting position and waiting before grabbing the sensor are not included in the next trial’s movement time.

### Boundaries and interruptions

If the mouse moves beyond the workspace during a drag, the sensor stops at the workspace boundary. Timing continues, and you can move the mouse back to correct the position.

Switching tabs, losing browser-window focus, resizing the window during an active trial, or losing pointer capture can interrupt the trial. An interrupted attempt does not count toward the 120 successful trials or the calculated means. Click **Retry interrupted trial** to repeat the condition. If you resized the window, restore its original size before continuing.

Scrolling the right-hand information column does not move the workspace or interrupt the trial by itself.

## Experimental design

| Setting | Value |
| --- | --- |
| Movement distance (A) | 150, 250, 350, 450, 550 CSS px |
| Target diameter (W) | 40, 60, 80, 100 CSS px |
| Conditions | 5 distance levels × 4 width levels = 20 |
| Repetitions | 6 successful trials per condition |
| Session length | 120 successful trials |
| Direction balance | 3 rightward and 3 leftward trials per condition; 60 of each overall |
| Sensor diameter | 24 CSS px |
| Pointer-to-sensor gain | 1:1 within the workspace |
| Input series | Mouse |

The session contains six blocks. Each block includes every A/W condition once, in a randomized order constrained by alternating rightward and leftward movement.

Movement is not restricted to a horizontal line. Each condition uses three pairs of opposite angles, allowing upward and downward components while preserving its exact start-to-target distance. Available angles depend on the workspace height and A so both endpoints remain visible. Rightward and leftward refer to the horizontal component of movement. Angles and directions are pooled into the same A/W condition for analysis.

## Timing and success criterion

Movement time (MT) is measured in milliseconds using `performance.now()`:

```text
MT = time of successful release − time of initial press on the sensor
```

The measurement starts on the initial pointer-down on the sensor, not on the first movement and not when the trial appears. It therefore includes any time spent holding the sensor before moving, along with correction and re-grab time after missed releases.

A trial succeeds when:

```text
distance(sensor center, target center) ≤ W / 2
```

A is the Euclidean distance between the prescribed starting center and target center, not the length of the actual dragged path.

## Fitts’ Law calculation and chart

The application uses the Shannon formulation:

```text
ID = log2(A / W + 1)
MT = a + b × log2(A / W + 1)
```

- **ID:** index of difficulty, in bits.
- **a:** regression intercept, in milliseconds.
- **b:** regression slope, in milliseconds per bit.

For each A/W condition, the application calculates:

```text
Mean MT = sum of its successful trial movement times / number of successful trials
```

At completion, the chart contains **20 condition means**, with **6 successful trials per point**:

- X-axis: **Index of Difficulty (bits)**.
- Y-axis: **Movement Time (ms)**, displaying condition mean MT.
- One **Mouse** series and its ordinary least squares regression line.
- The complete Shannon equation with the measured coefficients substituted for a and b.
- **R²**, calculated from those same 20 condition means. R² is undefined if all condition means are identical.

The Results section may show a provisional regression before completion. Use the final completed-session fit for reporting. The fitted coefficients are estimated from the data; they are not forced to have a positive slope.

### Reopen or save the chart

After closing the chart, click **View Fitts’ Law chart** below the completed workspace or in the Results section to open it again. Click **Download chart (SVG)** to save a vector image suitable for resizing and inserting into a report.

## Export data

In **Results**, select a dataset and click **Export CSV**:

| Dataset | Complete-session output | Partial-session output |
| --- | --- | --- |
| Individual trials | 120 successful-trial rows | Successful trials collected so far |
| Condition means | 20 rows, each with n = 6 | All 20 conditions; unmeasured means are blank and n = 0 |

Exports exclude interrupted attempts. They retain successful trials that required missed-release corrections. The interface separately shows the number of interrupted attempts.

### Individual-trial fields

| Fields | Meaning |
| --- | --- |
| `session_id`, `trial`, `attempt`, `block` | Session and trial identifiers; attempt numbers also advance for interrupted attempts |
| `A_px`, `W_px`, `ID_bits` | Prescribed distance, target diameter, and Shannon difficulty |
| `MT_ms`, `elapsed_ms` | Time to successful release; these values match for exported successful trials |
| `miss_count` | Number of unsuccessful releases before success |
| `valid`, `status`, `abort_reason` | Exported rows are successful: `1`, `success`, and an empty abort reason |
| `direction`, `angle_deg` | Horizontal direction and movement angle; 0° points right, 90° down, 180° left, and 270° up |
| `start_x`, `start_y`, `target_x`, `target_y`, `end_x`, `end_y` | Canvas-relative center coordinates in CSS pixels; the origin is at the top left |
| `workspace_width_px`, `workspace_height_px` | Workspace dimensions |
| `started_utc` | Trial start timestamp in UTC |
| `device_pixel_ratio`, `viewport_width`, `viewport_height` | Display and browser viewport metadata |
| `input`, `timing` | `mouse` and `pointerdown_to_successful_pointerup` |

### Condition-mean fields

`A_px`, `W_px`, `ID_bits`, `n`, `mean_MT_ms`, and `total_misses`.

CSV files use UTF-8 encoding. Filenames distinguish trial and condition exports, include the completed-trial count, and identify the session.

## Start another experiment

After completing the session:

1. Export any CSV files and download the chart that you want to retain.
2. Close the chart if it is open.
3. Click **Restart experiment** below the workspace.
4. Confirm the reset. Canceling preserves the current results.
5. Click **Start experiment** to begin a new randomized 120-trial session.

Restarting clears the old trial records, chart, statistics, and counters and generates a new session identifier. Old and new session data are not combined.

## Data storage

Experimental data exists only in the current page’s memory. The application does not upload trial data, save it to browser storage, or automatically attach results to the repository. Reloading, closing the page, or confirming a restart clears the session. Export the data and chart before doing any of these actions.

## Troubleshooting

| Situation | What to do |
| --- | --- |
| The experiment will not start | Enlarge the browser window until the whole workspace is visible and meets the minimum dimensions. |
| The sensor does not move | Press the left mouse button directly on the sensor and keep it held while dragging. Touch input is not supported. |
| Releasing does not complete the trial | Place the sensor center inside the target and release again. Timing continues until success. |
| A trial was interrupted | Restore focus and the original window size, then click Retry interrupted trial. |
| The chart button is disabled | Finish all 120 successful trials. Partial results can still be exported. |
| Results disappeared | Reloading or confirming a restart clears in-memory data. Use previously downloaded files; there is no session recovery. |