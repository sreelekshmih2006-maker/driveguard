#include <Wire.h>
#include <Adafruit_MPU6050.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP085.h>
#include <Adafruit_APDS9960.h>

// ---------- Pin Definitions ----------
#define I2C_SDA 21
#define I2C_SCL 22
#define TRIG_PIN 5
#define ECHO_PIN 18

// ---------- Sensor Objects ----------
Adafruit_MPU6050 mpu;
Adafruit_BMP085 bmp;
Adafruit_APDS9960 apds;

// ---------- MPU6050 Variables ----------
float accelX, accelY, accelZ;
float gyroX, gyroY, gyroZ;

// ---------- BMP180 Variables ----------
float pressure;
float altitude;
float altitudeGradient;
float previousAltitude = 0;
unsigned long previousAltitudeTime = 0;

// ---------- APDS9960 Variables ----------
uint8_t apdsProximity;

// ---------- HC-SR04 Variables ----------
float ultrasonicDistance;

// ---------- BendSense (BSI) Variables ----------
float yawRisk, lateralRisk, altitudeRisk;
float BSI;
float recommendedSpeed;

// =====================================================
// SETUP
// =====================================================
void setup() {
  Serial.begin(115200);
  while (!Serial) delay(10);

  Wire.begin(I2C_SDA, I2C_SCL);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  // ---- MPU6050 ----
  if (!mpu.begin()) {
    Serial.println("MPU6050 not detected!");
    while (1) delay(10);
  }
  mpu.setAccelerometerRange(MPU6050_RANGE_8_G);
  mpu.setGyroRange(MPU6050_RANGE_500_DEG);
  mpu.setFilterBandwidth(MPU6050_BAND_21_HZ);
  Serial.println("MPU6050 detected.");

  // ---- BMP180 ----
  if (!bmp.begin()) {
    Serial.println("BMP180 not detected!");
    while (1) delay(10);
  }
  Serial.println("BMP180 detected.");

  // ---- APDS9960 ----
  if (!apds.begin()) {
    Serial.println("APDS9960 not detected!");
    while (1) delay(10);
  }
  apds.enableProximity(true);
  Serial.println("APDS9960 detected.");

  previousAltitudeTime = millis();
}

// =====================================================
// LOOP
// =====================================================
void loop() {
  readMPU6050();
  readBMP180();
  readAPDS9960();
  readUltrasonic();
  computeBendSense();
  printSerialData();

  delay(500);
}

// =====================================================
// SENSOR READ FUNCTIONS
// =====================================================
void readMPU6050() {
  sensors_event_t a, g, temp;
  mpu.getEvent(&a, &g, &temp);

  accelX = a.acceleration.x;
  accelY = a.acceleration.y;
  accelZ = a.acceleration.z;

  gyroX = g.gyro.x;
  gyroY = g.gyro.y;
  gyroZ = g.gyro.z;
}

void readBMP180() {
  pressure = bmp.readPressure(); // Pa
  altitude = bmp.readAltitude(101560); // adjust reference pressure as needed (Pa)

  unsigned long now = millis();
  float dt = (now - previousAltitudeTime) / 1000.0; // seconds

  if (dt > 0) {
    altitudeGradient = (altitude - previousAltitude) / dt;
  }

  previousAltitude = altitude;
  previousAltitudeTime = now;
}

void readAPDS9960() {
  apdsProximity = apds.readProximity();
}

void readUltrasonic() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 30000); // 30ms timeout (~5m max range)

  if (duration == 0) {
    ultrasonicDistance = -1; // no echo received
  } else {
    ultrasonicDistance = (duration * 0.0343) / 2.0; // cm
  }
}

// =====================================================
// BENDSENSE RISK CALCULATION
// =====================================================
void computeBendSense() {
  // Yaw risk: based on rotation rate around Z axis (turning sharply)
  yawRisk = abs(gyroZ) / 5.0; // normalize, tune divisor to your sensor range
  yawRisk = constrain(yawRisk, 0.0, 1.0);

  // Lateral risk: based on sideways acceleration (X axis)
  lateralRisk = abs(accelX) / 9.8; // normalize against gravity
  lateralRisk = constrain(lateralRisk, 0.0, 1.0);

  // Altitude risk: based on rate of altitude change (climbing/descending fast)
  altitudeRisk = abs(altitudeGradient) / 2.0; // tune divisor as needed
  altitudeRisk = constrain(altitudeRisk, 0.0, 1.0);

  // Combined Bend Severity Index (weighted average — tune weights as needed)
  BSI = (yawRisk * 0.5) + (lateralRisk * 0.3) + (altitudeRisk * 0.2);
  BSI = constrain(BSI, 0.0, 1.0);

  // Recommended speed based on BSI (linear scale from 60 km/h down to 10 km/h)
  recommendedSpeed = 60.0 - (BSI * 50.0);
  recommendedSpeed = constrain(recommendedSpeed, 10.0, 60.0);
}

String getAlertName() {
  if (BSI < 0.3) return "SAFE";
  else if (BSI < 0.6) return "CAUTION";
  else if (BSI < 0.8) return "WARNING";
  else return "CRITICAL";
}

// =====================================================
// SERIAL OUTPUT
// =====================================================
void printSerialData()
{
  Serial.println();
  Serial.println("========================================");
  Serial.println("           BENDSENSE LIVE DATA");
  Serial.println("========================================");

  Serial.println("[MPU6050]");
  Serial.print("Acceleration X : "); Serial.print(accelX, 2); Serial.println(" m/s^2");
  Serial.print("Acceleration Y : "); Serial.print(accelY, 2); Serial.println(" m/s^2");
  Serial.print("Acceleration Z : "); Serial.print(accelZ, 2); Serial.println(" m/s^2");
  Serial.print("Gyroscope X    : "); Serial.print(gyroX, 3); Serial.println(" rad/s");
  Serial.print("Gyroscope Y    : "); Serial.print(gyroY, 3); Serial.println(" rad/s");
  Serial.print("Gyroscope Z    : "); Serial.print(gyroZ, 3); Serial.println(" rad/s");

  Serial.println();
  Serial.println("[BMP180]");
  Serial.print("Pressure       : "); Serial.print(pressure / 100.0, 2); Serial.println(" hPa");
  Serial.print("Altitude       : "); Serial.print(altitude, 2); Serial.println(" m");
  Serial.print("Altitude Rate  : "); Serial.print(altitudeGradient, 2); Serial.println(" m/s");

  Serial.println();
  Serial.println("[APDS9960]");
  Serial.print("Proximity      : "); Serial.println(apdsProximity);

  Serial.println();
  Serial.println("[HC-SR04]");
  Serial.print("Distance       : ");
  if (ultrasonicDistance < 0) {
    Serial.println("No object detected");
  } else {
    Serial.print(ultrasonicDistance, 1);
    Serial.println(" cm");
  }

  Serial.println();
  Serial.println("[BSI COMPONENTS]");
  Serial.print("Yaw Risk       : "); Serial.println(yawRisk, 3);
  Serial.print("Lateral Risk   : "); Serial.println(lateralRisk, 3);
  Serial.print("Altitude Risk  : "); Serial.println(altitudeRisk, 3);

  Serial.println();
  Serial.println("[BENDSENSE RESULT]");
  Serial.print("Bend Severity Index : "); Serial.println(BSI, 3);
  Serial.print("Alert Level         : "); Serial.println(getAlertName());
  Serial.print("Recommended Speed   : "); Serial.print(recommendedSpeed, 0); Serial.println(" km/h");

  Serial.print("Obstacle Status     : ");
  if (ultrasonicDistance > 0 && ultrasonicDistance <= 250) {
    Serial.println("CRITICAL - OBJECT WITHIN 2.5m");
  } else {
    Serial.println("CLEAR");
  }

  Serial.println("========================================");
}
