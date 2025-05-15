# Putting It All Together

## 1. Mounting the Main Frame to the Chassis

1. **Gently place the Upper Level Chassis** on top of the standoffs of the Lower Level Chassis.  
   - The **VESC should be towards the back** of the car.
   - Thread the **PPM cable** of the servo motor from the Lower Level Chassis through one of the **Platform Deck slots**.

   ![Upper Level Chassis placed on Lower Level Chassis.](img/together_NX_00.JPG)

   > ### ***Note that the Powerboard in the picture above is outdated. The new design looks different. Below is a picture of the new Powerboard and Layout.***
   >
   > ![Powerboard](img/PowerboardLayout.jpg)
2. **Secure the Platform Deck** to the standoffs on the Lower Level Chassis:
   - Use **three M3 screws**.
   - Optionally, **use a zip tie** to secure the USB cable from the lidar.

> ### **Warning:**
>
> The **driveshaft** along the length of the chassis rotates when the car moves. Ensure that all cables and wires are **kept away from rotating parts**, including the driveshaft, to prevent damage.

---

## 3. Connecting the Brushless Motor to the VESC

1. **Prepare three 4mm to 3.5mm bullet adapters**.

   ![Bullet adapters.](img/together07.png)

2. **Attach the adapters** to the **blue, yellow, and white wires** of the Brushless Motor.

   ![Brushless Motor wires.](img/together08.JPG)

3. **Connect the VESC wires to the motor**:
   - The **VESC has three labeled wires: A, B, and C**.

   ![VESCMKIII.](img/together10.jpg)

   - Connect the wires as follows:
     - **A → WHITE**
     - **B → YELLOW**
     - **C → BLUE**

   ![Brushless Motor connected to VESC.](img/together09.JPG)

> ### **Important:**
>
> If the vehicle moves **backwards** after flashing the VESC firmware, **swap the WHITE wire to "C" and the BLUE wire to "A"**.

---

## 4. Connecting the Battery to the VESC

1. **Plug the charge adapter** into the **battery plug**.

   ![Charge adapter connected to Lipo battery.](img/llchassis15.JPG)

   > ### **Danger: Battery**
   >
   > **Ensure RED (POWER) and BLACK (GROUND) are connected correctly.**  
   > Incorrect connections may result in **fire**.

2. **Connect the charge adapter to a TRX to XT90 adapter**.

   ![Connecting TRX to XT90 adapter.](img/llchassis16.JPG)

3. **Final battery connection setup**:

   ![TRX to XT90 adapter installed.](img/llchassis17.JPG)

4. **Final car setup after battery connection**:

   ![Battery connected to VESC.](img/BatteryConnected.jpg)

---

## 5. Connecting the NVIDIA Jetson NX to the VESC

1. The **Jetson NX** needs power from the **Powerboard**.  
   - Use a **male barrel jack to male barrel jack cable to connect the Jetson to the Powerboard** (2.5x5.5mm power jack).
   - ***Note: The **center pin is POWER*****.

   > ### **Warning: NVIDIA Power**
   >
   > Barrel jack connections **are not standardized**. **Do not plug in a power supply where the center pin is ground**, as this may cause damage.

   ![Jetson NX power supply connected to Powerboard.](img/JetsonConnect.jpg)

   > ### Note: The powerboard output 19v for the Jetson. If your computing unit does not accept 19v, use a voltage converter like a **Buck Converter** to convert the 19v to whatever your computing unit needs

---

## 6. Connecting the Lidar

1. The lidar comes with **two long cables**. **Manage them carefully** by looping them under the slots on the **Platform Deck**.
2. **Connect the Lidar to the NVIDIA Jetson NX**:
   - **UTM-30LX** → Plug into the **USB hub**.
   - **UST-10LX** → Plug into the **Ethernet port** of the Jetson NX.

3. **Connect the Lidar’s power supply**:

> **Danger:**  
> **BROWN is POWER, and BLUE is GROUND.** **Mixing these up will fry the Lidar**. Double-check the Hokuyo label for correct wiring.

   ![Lidar power connected to terminal block.](img/LidarConnect.jpg)

---

## 7. Attaching the PPM Cable

1. **Locate the PPM cable** (one end is **white**, the other is **black**).

   ![PPM cable.](img/llchassis21.JPG)

2. **Take three header pins**.

   ![Header pins.](img/llchassis18.JPG)

3. **Plug them into the Servo wires**.

   ![Header pin connected to Servo cable.](img/llchassis19.JPG)

4. **Connect the PPM cable to the Servo**.

   > **Danger:**  
   > **BROWN is GROUND.** Ensure it connects to the **BLACK wire of the Servo Cable**.

   ![PPM cable connected to Servo cable.](img/llchassis20.JPG)

5. **Connect the PPM cable to the VESC**.

   ![PPM cable plugged into VESC.](img/together_NX_04.JPG)

---

## 8. Final Touches

1. **Connect a micro USB** (white cable) from the **VESC to the USB hub**.

   ![Micro USB connected to VESC.](img/together_NX_08.JPG)

2. **Screw on the antennas** to the Antenna Terminals.

---

## 9. Voila! 🎉

Your **final RoboRacer** should look like this:

   ![Final assembled vehicle.](img/final.JPG)
