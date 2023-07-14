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
- Connect to `MOXA Wi-Fi network` 
- Change Wi-Fi network adapter IPv4 address to `192.168.0.xxx` (Anything but 7)
  - 192.168.0.7 is the UR5's IPv4 address



!> Note that `MOXA Wi-Fi network` does not have internet access 

?> Connect to ethernet port to have internet access while connected to MOXA 


