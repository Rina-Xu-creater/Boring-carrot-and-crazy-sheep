# Boring-carrot-and-crazy-sheep

/*
 * TinyFilmFestival - Simple Distance Sensor Play/Pause Example
 * 
 * This example demonstrates using an HC-SR04 ultrasonic distance sensor to toggle 
 * play/pause state of an animation. The animation starts playing automatically,
 * and distance below threshold toggles between playing and paused states.
 * 
 * Hardware Required:
 * - Arduino UNO R4 WiFi with built-in LED Matrix
 * - HC-SR04 ultrasonic distance sensor connected to pins 14 (trigger) and 15 (echo)
 *
 * Uses Arduino LED Matrix Editor https://ledmatrix-editor.arduino.cc/ 
 */

#include "TinyFilmFestival.h"
#include "HCSR04.h"
#include "boring_sheeps.h"
#include "boring_and_boring_waves.h"


// Create instance of TinyFilmFestival
TinyFilmFestival film;

// Create Animation object
Animation idleAnim = boring_sheeps;
Animation wavesAnim = boring_and_boring_waves;

// Distance sensor setup
int triggerPin = 14;          // A0 Trigger pin 
int echoPin = 15;             // A1 Echo pin 
int maxDistance = 200;        // Maximum distance to measure (cm)
UltraSonicDistanceSensor distanceSensor(triggerPin, echoPin, maxDistance);

// Distance control variables
float distanceThreshold = 5.0;
float distanceThreshold1 = 30.0;  // Threshold for activation (cm)
float distanceThreshold2 = 75.0;  
int sensorInterval = 100;        // How often to read sensor (ms)
unsigned long lastSensorRead = 0;      // Last sensor reading time
float currentDistance = 0.0;           // Variable to store distance reading

void setup() 
{
    // Initialize serial for debug output
    Serial.begin(9600); 
    
    // Initialize the LED matrix
    film.begin();
    
    // Start with animation playing in loop mode
    film.startAnimation(idleAnim, LOOP);
    Serial.println("Stating with idle animation");
}

void loop() 
{
    // Read distance value with timing control
    float distance = readDistance();
    
   
    if (distance >= 0)
    {
        Serial.print("Distance : ");
        Serial.print(distance);
        Serial.println(" cm");

  if (distance < distanceThreshold) 
        {
            film.pause();
            Serial.println("Animation paused");
        }
        else 
        {
            film.resume();
            Serial.println("Animation resumed");
        }
        // Very Close Range: 0cm to 30cm
        if ((distance >= 0) && (distance < distanceThreshold1))
        {
            film.startAnimation(idleAnim, LOOP);
            Serial.println("Playing Fiz (Very Close)");
        }
        // 
        else if ((distance >= distanceThreshold1) && (distance < distanceThreshold2))
        {
            film.startAnimation(wavesAnim, LOOP);
            Serial.println("Playing Go (Close)");
        }
    }
    
    // Update the animation frame
    film.update();
}

// Reads distance from the HC-SR04 sensor at specified intervals
// Function returns float since HC-SR04 can measure partial centimeters
float readDistance()
{
    unsigned long currentTime = millis(); 
    
    // Only read sensor if interval has elapsed
    if (currentTime - lastSensorRead >= sensorInterval)
    {
        currentDistance = distanceSensor.measureDistanceCm();
        lastSensorRead = currentTime;
    }

    
    return currentDistance;
}
