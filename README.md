# Getting Started

> Link to Dashboard [UR5-Web](https://ur5reactapp.vercel.app/)
## Overview
This documentation covers the following topics:
- Condition Monitoring (CM) 
- Predictive Maintenance (PM)
- Data analysis and Big Data
  - Data collection

and how they are implemented in the Dashboard.

## Framework and Libraries
`React.js` is used as the framework for the Dashboard.

?> `Node.js` is required to use React.js and its libraries

The following libraries are used:
- `react-router-dom` for routing
- `tailwindcss` for styling
- `fontawesome` for icons
- `react-chartjs-2` for charts
- `mui/material` for UI components (such as sliders)
- `sweetalert2` for popup alerts
- `zendeskgarden/react-avatars` for avatars
- `axios` for HTTP requests
- `chartjs` for line charts 
- `echarts-for-react` for gauge charts
- `moment` for date and time 
- `react-toastify` for toast notifications
  - `useNotificationCenter` addon for notification center
- `styled-components` for component styling

## Connectivity to UR5 Robot 
 `Node-RED` is used to connect to the UR5 Robot via TCP/IP
- `UR5 Robot` is firstly connected to a `ethernet switch` which is connected to the `MOXA Client` via an ethernet cable
![logo](/img/switchclientconnection.jpg)
- `MOXA Client` then connects to `MOXA Access Point` wirelessly 

On your own computer:
### Wireless
- Connect to `MOXA Wi-Fi network` 
- Change Wi-Fi adapter IPv4 address to `192.168.0.xxx` (Anything but 7)
  - 192.168.0.7 is the UR5's IPv4 address

  !> Note that `MOXA Wi-Fi network` does not have internet access 

  ?> Connect to ethernet port to have internet access while connected to MOXA 

### Wired
- Connect the GREEN Ethernet Cable labelled `X000 HMI P2C` to your computer \(Initially connected to the switch as seen in the above image)
- Change ethernet adapter IPv4 address to `192.168.0.xxx` (Anything but 7)
### Node-RED
- Start Node-RED with `node-red` in command prompt
- Run the Data Collection flow 
  ![logo](/img/datacollectionflow.jpg)

  !> Restart Node-RED on command prompt if mysql database disconnected

  ?> Database is subject to change, mysql database not being able to connect indicates that it should be re-created

>UR5 Robot is now connected to the computer and real-time data is ready to be monitored

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


  






