# SOOPERCHARGE your little friend
![alt text](https://github.com/SlyFox-Asia/looi-soopercharged/blob/ebfa4fc97cf09908a15f0afcbd31393fc01ab911/SOOPERCHARGE.png)
Project SOOPERCHARGE is the first RE project for LOOI, aiming to render it fully open source.
This repo, while still empty-ish, is going to hold almost all info I know about the robot - and some extra content, such as leaks.

# About RE...
The current goal is to RE how the LOOI App communicates with the robot in order to create custom animations and reimplement everything on a possibly modified client.

LOOI robot operates on a sequence-based Bluetooth Low Energy (BLE) protocol.
The primary control interface is the fe00 characteristic, which accepts multi-byte command strings to coordinate motors, LEDs, and animations simultaneously. Unlike simple direct-drive robots, LOOI uses a sort of "Script Mode" logic where commands function as frames in an animation sequence rather than instantaneous electrical signals.

The first byte is a rolling counter (`00`-`FF`). The robot discards any packet with a duplicate or out-of-order sequence ID to prevent command flooding.

The second byte determines the parsing logic (e.g., `0e` for complex animations, `00` for raw motor control). Controlling the RGB strip is possible with the 0e mode, but I haven't figured how to do it correctly yet.

Most commands sent to fe00 follow this hypotetical 9-to-11 byte structure:

```
[SEQ] [MODE] [TIME] [ R ] [ G ] [ B ] [RES] [TARGET] [VAL] [DUR] [END]
```

  * **`SEQ`**: Sequence ID. Must increment for every new command.
  * **`MODE`**: The command type.
      * `00`: Fine Adjustment (Neck/Slow move).
      * `02`: Rotation.
      * `0e`: Animation Script (Master Mode for Light + Move).
      * `10`: Still figuring it out.
  * **`TIME`**: Execution timestamp or frame index (e.g., `10`, `21`, `7d`).
  * **`R G B`**: Red, Green, Blue brightness values (`00`-`FF`).
  * **`RES`**: Reserved/Spacer (Usually `00`).
  * **`TARGET`**: Subsystem ID.
      * `00`: Wheel Base.
      * `01`: Neck Servo.
  * **`VAL`**: Magnitude. Motor Speed (for Wheels) or Position Angle (for Neck).
  * **`DUR`**: Duration of the action (ms/ticks). `00` often implies "Hold".
  * **`END`**: Footer.

### **Known Characteristics**

  * **`fe00`**: Main Command Interface (Write).
  * **`ff02`**: Primary Motor Drive (Boost mode - When talking directly to it, sending `FF` would supposedly change the moving direction - There is also a way to let it rotate on its own, but I have no idea what's the code for it and am still researching on it).
  * **`fed0`**: Primary Motor Drive (Slow mode).
  * **`fed1`**: Neck Motor.
  * **`fed2`**: Headlight/Torch Toggle (When sending commands directly to it, `00` = Off, `03` = On).
  * **`fed9`**: Sensor Data (Read Only).

# About leaks...
You can check out leaked material (or possibly content that didn't make it to production) in the leaks folder. For audio files, check out the audio folder instead.
All content hosted here is taken directly from LOOI's Android app.

# Credits

u/revned911 (reddit) for initial findings on the topic

# Want to contribute?
Please contact me personally on Telegram @SplattyDoesStuff. I'm looking forward to working with anyone who has more competences than me - especially on the code side :)
