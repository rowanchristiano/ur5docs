# Getting Started

> Link to Dashboard [UR5-Web](https://ur5reactapp.vercel.app/)

## Overview

This documentation covers the following topics:

- `Condition Monitoring (CM)`
- `Predictive Maintenance (PM)`
- `Data Analysis and Big Data`
  - `Data Collection`

and how they are implemented in the Dashboard.

## Connectivity to UR5 Robot

`Node-RED` is used to connect to the UR5 Robot via TCP/IP

### Telematic Data Collector (TDC-E)
- `TDC-E` is connected to a 24V DC power supply and then connected to the `UR5` via Eth1 port (refer to image, port '4')
  - ![TDC-E front](/IMG/TDC-E.png 'TDC-E Front')
- `TDC-E` has to be configured to be connected to WLAN 
- Starting the 

### LAN

- `UR5 Robot` is firstly connected to a `ethernet switch` which is connected to the `MOXA Client` via an ethernet cable
  ![logo](/img/switchclientconnection.jpg 'Connection Diagram')
- `MOXA Client` then connects to `MOXA Access Point` wirelessly

On your own computer:

- Connect to `MOXA Wi-Fi network`
- Change Wi-Fi adapter IPv4 address to `192.168.0.xxx` (Anything but 7)

  - 192.168.0.7 is the UR5's IPv4 address

  !> Note that `MOXA Wi-Fi network` does not have internet access

  ?> Connect to ethernet port to have internet access while connected to MOXA

#### Wired

- Connect the GREEN Ethernet Cable labelled `X000 HMI PLC` to your computer \(Initially connected to the switch as seen in the above image)
- Change ethernet adapter IPv4 address to `192.168.0.xxx` (Anything but 7)

### Node-RED

- Start Node-RED with `node-red` in command prompt
- Run the Data Collection flow
  ![logo](/img/datacollectionflow.jpg)

  !> Restart Node-RED on command prompt if mysql database disconnected

  ?> Database is subject to change, mysql database not being able to connect indicates that it should be re-created

> UR5 Robot is now connected to the computer and real-time data is ready to be monitored

## Database

### MySQL

- `MySQL` is the primary database used in the dashboard
- The `MySQL` server is hosted by `DigitalOcean` and can be accessed from any computer through valid connection credentials
  - Other hosting services can also be used

### Database structure

Tables

- History
  - Contains data collected every 10 minutes when Node-RED is running
  - Data is used for [data analysis]()
- Realtime

  - Contains real-time data collected every second
  - Data is used for real-time portion of the dashboard
    <details>
      <summary>SQL Creation Query</summary>

    ```sql
    CREATE TABLE realtime (
    collected_on DATETIME DEFAULT CURRENT_TIMESTAMP,
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    operating_time BIGINT,
    physical_robot_connected INTEGER,
    real_robot_enabled INTEGER,
    robot_power_on INTEGER,
    emergency_stopped INTEGER,
    protective_stopped INTEGER,
    program_paused INTEGER,
    program_running INTEGER,
    robot_mode TEXT,
    robot_mode_description TEXT,
    control_mode TEXT,
    control_mode_description TEXT,
    target_speed_fraction REAL,
    speed_scaling REAL,
    target_speed_fraction_limit REAL,
    joint_position_actual_0 REAL,
    joint_position_target_0 REAL,
    joint_position_actual_degree_0 REAL,
    joint_position_target_degree_0 REAL,
    joint_speed_actual_0 REAL,
    joint_current_actual_0 REAL,
    joint_voltage_actual_0 REAL,
    joint_motor_temperature_0 REAL,
    joint_mode_0 TEXT,
    joint_mode_description_0 TEXT,
    joint_position_actual_1 REAL,
    joint_position_target_1 REAL,
    joint_position_actual_degree_1 REAL,
    joint_position_target_degree_1 REAL,
    joint_speed_actual_1 REAL,
    joint_current_actual_1 REAL,
    joint_voltage_actual_1 REAL,
    joint_motor_temperature_1 REAL,
    joint_mode_1 TEXT,
    joint_mode_description_1 TEXT,
    joint_position_actual_2 REAL,
    joint_position_target_2 REAL,
    joint_position_actual_degree_2 REAL,
    joint_position_target_degree_2 REAL,
    joint_speed_actual_2 REAL,
    joint_current_actual_2 REAL,
    joint_voltage_actual_2 REAL,
    joint_motor_temperature_2 REAL,
    joint_mode_2 TEXT,
    joint_mode_description_2 TEXT,
    joint_position_actual_3 REAL,
    joint_position_target_3 REAL,
    joint_position_actual_degree_3 REAL,
    joint_position_target_degree_3 REAL,
    joint_speed_actual_3 REAL,
    joint_current_actual_3 REAL,
    joint_voltage_actual_3 REAL,
    joint_motor_temperature_3 REAL,
    joint_mode_3 TEXT,
    joint_mode_description_3 TEXT,
    joint_position_actual_4 REAL,
    joint_position_target_4 REAL,
    joint_position_actual_degree_4 REAL,
    joint_position_target_degree_4 REAL,
    joint_speed_actual_4 REAL,
    joint_current_actual_4 REAL,
    joint_voltage_actual_4 REAL,
    joint_motor_temperature_4 REAL,
    joint_mode_4 TEXT,
    joint_mode_description_4 TEXT,
    joint_position_actual_5 REAL,
    joint_position_target_5 REAL,
    joint_position_actual_degree_5 REAL,
    joint_position_target_degree_5 REAL,
    joint_speed_actual_5 REAL,
    joint_current_actual_5 REAL,
    joint_voltage_actual_5 REAL,
    joint_motor_temperature_5 REAL,
    joint_mode_5 TEXT,
    joint_mode_description_5 TEXT
    );
    ```

    </details>

- Logs
  - Contains notification messages for joint's threshold exceeded
  - Contains PM notifications for when UR5 joint is in critical condition

### API

`Express.js` is a `Node.js` server framework is used to build APIs to

- `get` data from the database
- `post` data to the database

Express server runs on a serverless function on `Vercel` (Cloud platform mainly for deploying web apps and services)

  <details>
    <summary>Initialization example</summary>

  ```js
  const mysql = require("mysql2");

  const mysqlPool = mysql.createPool({
    host: "yourdatabaseurl",
    port: 12345,
    user: "root",
    password: "password",
    database: "databasename",
  });

  const express = require("express");
  const app = express();
  ```

  </details>
  
  <details>
    <summary>Get example</summary>

  ```js
  // Define the route to fetch data from the history table, limited to 500 rows
  app.get("hist/", async (req, res) => {
    mysqlPool.getConnection((err, connection) => {
      if (err) {
        console.error("Error establishing MySQL connection:", err);
        res.status(500).json({ error: "Internal server error" });
        return;
      }

      connection.query(
        "SELECT * FROM history order by collected_on asc limit 500",
        (error, results) => {
          connection.release();
          if (error) {
            console.error("Error fetching MySQL data:", error);
            res.status(500).json({ error: "Internal server error" });
            return;
          }

          console.log("Data fetched!");
          res.json(results);
        }
      );
    });
  });
  ```

</details>

<details>
<summary>Post Example</summary>

```js
// API endpoint for updating threshold settings in global_settings table
app.post("/threshold/:joint", (req, res) => {
  const { threshold } = req.body;
  const joint = req.params.joint;
  // Convert the threshold array to a JSON string
  const thresholdJson = JSON.stringify(threshold);
  // Get a connection from the pool
  mysqlPool.getConnection((err, connection) => {
    if (err) {
      console.error("Error establishing MySQL connection:", err);
      res.status(500).json({ error: "Internal server error" });
      return;
    }

    connection.query(
      `UPDATE global_settings SET threshold = ? WHERE id = ${joint};`,
      [thresholdJson],
      (error, results) => {
        connection.release();
        if (error) {
          console.error("Error clearing and resetting table:", error);
          res.status(500).json({ error: "Internal server error" });
          return;
        }

        console.log(`Joint ${joint} threshold updated! `, { threshold });
        res.json(results);
      }
    );
  });
});
```

</details>

## Digital Ocean
### Prerequisites - Github Student
Before creating a Digital Ocean account, a `Github` account with <a href='https://education.github.com/pack'>`Github Student Developer Pack`</a> is needed to create a Digital Ocean account with $200 Credits that can be used to create the database.
- Verify using Singapore Polytechnic Admin Card and ichat email

### Creating Database
After Creating DigitalOcean account and adding payment method,
- Click on `Deploy a database`
![DigitalOceanHome](/img/DigitalOceanHome.jpg)
- Choose `Singapore` 
  ![](/img/DatabaseCountry.jpg)
- Choose `MySQL`
  ![](/img/DatabaseType.jpg)
- Use the following configuration
![](<WhatsApp Image 2023-10-13 at 16.19.24_fe010b4a.jpg>)
![Alt text](<WhatsApp Image 2023-10-13 at 16.19.48_a27f18b8.jpg>)
![Alt text](<WhatsApp Image 2023-10-13 at 16.21.54_1cd490f0.jpg>)
Click on `Create Database Cluster`
  Wait for database to finish setting up
Install <a href='https://dev.mysql.com/downloads/workbench/'>`MySQLWorkbench`</a>
## Framework and Libraries

`React.js` is used as the javascript **framework** for the Dashboard.

?> `Node.js` is required to use `React.js` and its **libraries**

The following libraries are used:

- `react-router-dom` for routing
- `tailwindcss` for styling
- `fontawesome` for icons
- `react-chartjs-2` for charts
- `mui/material` for UI components (such as sliders)
- `sweetalert2` for popup alerts
- `status-indicator` for status indicator
- `axios` for HTTP requests
- `chartjs` for line charts
- `echarts-for-react` for gauge charts
- `moment` for date and time
- `react-toastify` for toast notifications
  - `useNotificationCenter` addon for notification center
- `styled-components` for component styling
