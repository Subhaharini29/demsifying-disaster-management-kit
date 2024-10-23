# demsifying-disaster-management-kit
IoT Sensor Integration (Node.js with MQTT or HTTP):
const express = require('express');
const mqtt = require('mqtt');

const app = express();
const mqttClient = mqtt.connect('mqtt://broker.hivemq.com');

mqttClient.on('connect', () => {
    mqttClient.subscribe('disaster/earthquake');
});

mqttClient.on('message', (topic, message) => {
    console.log(`Received message: ${message.toString()}`);
    // Process and store in the database
});

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
Frontend Interface (React.js):
import React, { useState, useEffect } from 'react';
import { Map, Marker } from 'react-leaflet';

const DisasterMap = () => {
    const [disasters, setDisasters] = useState([]);
    
    useEffect(() => {
        // Fetch disaster data from backend
        fetch('/api/disasters')
            .then(res => res.json())
            .then(data => setDisasters(data));
    }, []);
    
    return (
        <Map center={[0, 0]} zoom={2}>
            {disasters.map(disaster => (
                <Marker
                    key={disaster.id}
                    position={[disaster.lat, disaster.lng]}
                    title={disaster.type}
                />
            ))}
        </Map>
    );
};

export default DisasterMap;
Backend for Alerts and Notifications (Node.js with Twilio API):
const twilio = require('twilio');

const accountSid = 'YOUR_ACCOUNT_SID';
const authToken = 'YOUR_AUTH_TOKEN';
const client = new twilio(accountSid, authToken);

function sendAlert(message, phoneNumber) {
    client.messages.create({
        body: message,
        from: 'YOUR_TWILIO_PHONE_NUMBER',
        to: phoneNumber
    })
    .then(message => console.log(`Alert sent with ID: ${message.sid}`))
    .catch(err => console.error(err));
}

// Call sendAlert whenever a disaster is detected
sendAlert('Earthquake detected in your area. Stay safe!', '+1234567890');
Database for Storing Disaster Data (MongoDB):
const mongoose = require('mongoose');

const disasterSchema = new mongoose.Schema({
    type: String,
    lat: Number,
    lng: Number,
    timestamp: { type: Date, default: Date.now }
});

const Disaster = mongoose.model('Disaster', disasterSchema);

function saveDisaster(type, lat, lng) {
    const disaster = new Disaster({ type, lat, lng });
    disaster.save()
        .then(() => console.log('Disaster saved'))
        .catch(err => console.error(err));
}

// Call saveDisaster when a disaster is detected
saveDisaster('Earthquake', 37.7749, -122.4194);
