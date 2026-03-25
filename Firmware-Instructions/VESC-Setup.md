# 1. Configuring the VESC

> ⚠️ **Important Safety Tips**
>
> - Put your car on an elevated stand so that its wheels can turn without it going anywhere. If you don’t have an RC car stand, you can use a box
> - Make sure you hold on to the car while testing the motor to prevent it from flying off the stand.
> - Make sure there are no objects (or people) in the vicinity of the wheels while testing.
> - Use a fully-charged LiPO battery instead of a power supply to ensure the motor has enough current to spin up.

## Equipment Required

- Fully built vehicle  
- Box or [Car stand](https://a.co/d/03Yckzs3) to put vehicle on  
- Laptop/computer (does not need to be running Linux)

**Approximate Time Investment:** 1 hour

> ℹ️ **Note**
>
> If using the VESC mkIV (e.g., hardware based on VESC 4.12), see [this repository](https://github.com/f1tenth/vesc_firmware) for more details on building firmware and prebuilt versions for different hardware.

---

## 1. Installing the VESC Tool

We need to configure the VESC so that it works with our motor and vehicle transmission.

Before you start, install the [VESC Tool](https://vesc-project.com/vesc_tool).  
You’ll need to register for an account to download it. Add the free tier tool to your cart (only email required). After checkout, a download link will be sent to your email.

Versions are available for Linux, Windows, and macOS.

---

## 2. Powering the VESC

First, power the VESC:

- Plug in the battery and ensure correct polarity.
- You do **not** need to turn on the Powerboard for configuration.

![VESC Setup](img/vesc/vesc01.JPG)

Next:

- Unplug the USB cable from the Jetson NX
- Plug it into your laptop running VESC Tool (a longer cable may help)

![VESC USB Connection](img/vesc/vesc02.JPG)

---

## 3. Connecting the VESC to Your Laptop

- Launch **VESC Tool**
- On the Welcome page, click **AutoConnect** (bottom-left)
- Connection status appears in the bottom-right

![Connect VESC](img/vesc/connect.png)

---

## 4. Updating the Firmware on the VESC

Update the firmware on the VESC.

### For VESC Tool versions after Mar. 31, 2021:
- Use default firmware
- Enable servo output:
  - **App Settings → General → Enable Servo Output**

![Servo Output](img/vesc/servo.png)

### For versions before 2.05:
- Go to **Firmware** tab
- Check **Show non-default firmwares**
- Select `VESC_servoout.bin`
- Click the **download arrow button** to upload firmware

![Firmware Update](img/vesc/firmware.png)

---

## 5. Uploading the Motor Configuration XML

- Select **Load Motor Configuration XML**
- Choose the XML file from [here](https://drive.google.com/file/d/1-KiAh3hCROPZAPeOJtXWvfxKY35lhhTO/view?usp=sharing)
- Click **Write Motor Configuration** (down arrow + M)

> ⚠️ You must click this button whenever you change motor configuration.

![Upload XML](img/vesc/xml.png)

---

## 6. Detecting and Calculating Motor Parameters

- Go to **Motor Settings → FOC**
- Follow the 4-step detection process
- Motor will spin and make noise — ensure wheels are clear

![Detect Motor](img/vesc/detect_motor.png)

After detection:
- Fields turn green
- Click **Apply**
- Click **Write Motor Configuration**

![Apply Motor Params](img/vesc/apply_motor.png)

---

## 7. Changing Openloop Hysteresis and Time

- Go to **Sensorless** tab
- Set:
  - **Openloop Hysteresis = 0.01**
  - **Openloop Time = 0.01**
- Click **Write Motor Configuration**

![Openloop Settings](img/vesc/open_loop.png)

---

## 8. Tuning the PID Controller

To monitor RPM:

- Go to **Data Analysis → Realtime Data**
- Click **Stream Realtime Data (RT button)**
- Open **RPM tab**

![Realtime Data](img/vesc/realtime.png)

### Step Response Testing

- Set target RPM (2000–10000)
- Click **Play** to start motor
- Click **Stop/Anchor** to stop

![Step Response](img/vesc/response.png)

### Goal

- Fast rise time
- Minimal steady-state error
- No oscillation

### Adjust PID Gains

- Go to **Motor Settings → PID Controllers**
- Tune Speed PID gains

> Tip: If oscillations occur, adjust **Speed PID Kd Filter**

![PID Gains](img/vesc/pid_gains.png)

---

## 9. Changing the Hardware Speed Limit

- Go to **Motor Settings → General**
- Adjust **Max ERPM** (forward/backward)

![ERPM Settings](img/vesc/erpm.png)

> ⚠️ **Important**
>
> See the *Odometry Tuning* section in your software stack setup to understand how velocity maps to ERPM and determine safe limits.
