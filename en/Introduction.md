---
title: Introduction
description: 
published: true
date: 2026-05-27T15:17:01.145Z
tags: 
editor: markdown
dateCreated: 2026-05-27T15:17:00.612Z
---

## What is the CRANIX Server
The CRANIX Server is a central authentication, authorization, file, printer, and database server. The current CRANIX Server 4.5 is based on the openSUSE Leap 15.6 distribution.

## Naming Conventions
Unfortunately, not all operating systems and file systems handle uppercase and lowercase characters in the same way.
To avoid problems, we have defined the following naming convention:

> All object names, except GROUP NAMES, are written exclusively in lowercase letters. GROUP NAMES are written exclusively in uppercase letters.
{.is-info}



## The Administration Interface
The administration interface can be accessed internally via the URL http://admin. This URL is redirected to the secure URL "https://admin.<domainname>".

After a successful login, you will enter the administration interface (cockpit) of the CRANIX server. It consists of four parts:

- In the upper left corner, you can see the CRANIX logo. It also indicates whether you are logged into a CRANIX or CEPHALIX server. On a mobile device or in a narrow view, the menu button is located here. In the normal view, you can collapse the menu bar with a single click on the CRANIX logo and expand it again with a double click.
- In the toolbar, the name of the logged-in user and the room in which they are logged in are visible. On the right side, depending on the roles and permissions of the logged-in user, up to 3 buttons are available:
    - **Fetch all objects from the server** Reloads the data
    - **Edit user settings** Here you can individually change the language, appearance, and behavior of the cockpit. These settings are stored on the respective device. This allows you to create different settings for tablet, mobile phone, and computer.
    - **Log out**
- On the left side, the menu bar is displayed. Which menu items a user can see depends on their permissions. These, in turn, depend on the user's role. The active menu item is highlighted with a different color.
- In the center, the selected menu item is displayed. Most menu items are structured as follows:
    - **Title** This is especially important in narrow views, as the menu bar is not visible there.
    - **Search field** In most menu items, many objects (users, computers, rooms, devices, etc.) are displayed. In the search field, you can search for objects in all fields, including those that are not visible.
    - To the right of the search field, or on mobile devices in a "Floating Action Button", there are buttons for performing various actions:
			- Use the plus sign to create a new object.
			- The 3-dot menu ⋮ can only be applied to selected objects.
  			- With the ⋮⋮⋮ symbol, you can select which fields should be displayed for each object table. This setting is also permanently stored on the respective device.
  
  ![cocpit_toolbar_basic.png](/cocpit_toolbar_basic.png)