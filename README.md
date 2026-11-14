# QuickVolt firmware releases

Firmware images for QuickVolt bench power supplies. Source is not published here; each release
lists the build it came from.

Download from [Releases](../../releases). Current: **0.1.0-beta.1** (beta: expect rough edges).

## Updating a board that already runs QuickVolt firmware

Your calibration, Wi-Fi settings, name and schedules are kept. If the new firmware fails its
self-test after the restart, the board goes back to the previous firmware on its own.

1. Download `quickvolt-<version>.bin` and check it:
   `sha256sum quickvolt-<version>.bin` (macOS: `shasum -a 256`), compare with the `.sha256` file.
2. Turn the output off.
3. Install it, one of two ways:
   - **From the board's web page** (boards already on 0.1.0-beta.1 or later): click the firmware
     button in the header, pick the file, confirm. Keep that tab open until it says "Updated".
   - **From a terminal** (use this for the first update to 0.1.0-beta.1: older pages often stall
     mid-upload). Close every browser tab showing the board first, then:
     ```
     curl -u admin:<board password> --data-binary @quickvolt-0.1.0-beta.1.bin http://<board ip>/ota
     ```
     On Windows PowerShell type `curl.exe`, not `curl`. It replies `OTA OK - rebooting into new firmware` after 10-20 s. The board is back about
     30 s later.
4. If the update is refused with `low memory`, turn Bluetooth off in the board's settings, update,
   then turn it back on.

## After updating: check the input reading

Power the board from a supply whose voltage you know and compare **Input (Vin)** on the web page.

- Within a few %: nothing to do.
- About 0.74x your supply (20 V reads ~14.9 V): your board has the 120k Vin/Vext dividers, and its
  stored calibration predates that setting. Record it once:
  ```
  curl -u admin:<board password> -X POST http://<board ip>/api/cal/vsense -d '{"ratio":0.076923}'
  ```
  Until then the output is capped about 1.5 V under the (under-read) input, so it fails safe.

## Reporting a problem

Note the version (the firmware dialog on the web page, or `http://<board ip>/api/status`), what you did, and what the
page showed. After an unexpected restart, `/api/status` shows the reset reason; include it.
