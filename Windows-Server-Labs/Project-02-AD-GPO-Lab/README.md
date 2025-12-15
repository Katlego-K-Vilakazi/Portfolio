🖥️ PART 1 — Create OUs, Users, and Groups (Server 2022 VM)
1. Open Active Directory Users and Computers (ADUC)

Start → search “Active Directory Users and Computers” → open.

2. Create OUs

Right-click your domain → New → Organizational Unit
Create:

Kelly Sales Users

Phil Marketing Users

Ryan Managers

3. Create Users

For each OU:

Example for KatlegoBoss:

Right-click Managers → New → User

First name: Ryan

Last name: Boss

Username: Ryan.Team1Vee.intenal

Password: MSPress#1

Check:
✔ Password never expires
✔ User cannot change password (optional)

Repeat for:

Kelly Salesbody (in Sales Users)

Phil Marketer (in Marketing Users)

4. Create Groups

Inside each OU → right-click → New → Group

Create:

OU	Group Name	Type	Members
 Managers	Ryan Manager	Global Security	Add Ryan
 Sales Users	Kelly Sales	Global Security	Add Kelly
 Marketing Users	Phil Marketing	Global Security	Add Phil

To add a user to the group:
Right-click group → Properties → Members → Add → type user → OK.

📸 SCREENSHOTS NEEDED — PART 1
Screenshot 2a

📍 Open Managers OU
It must show:

Ryan (user)

Manager (group)

AND

Open the group’s Members tab showing Ryan inside.

Screenshot 2b

📍 Open Marketing Users OU
Show:

Phil Marketer (user)

Marketing (group)

AND

Group Members tab showing Phil Marketer.

Screenshot 2c

📍 Open Sales Users OU
Show:

Kelly (user)

Sales (group)

AND

Group Members tab showing Kelly Salesbody.

🖥️ PART 2 — Create Shared Folders and NTFS Permissions (Server 2022)
5. Create the main share

Go to *C:*
Right-click → New → Folder → name it:

Share

Right-click folder → Properties → Sharing tab → Advanced Sharing
✔ Share this folder
✔ Permissions → allow Everyone = Full Control
Click OK.

Copy the Network Path shown (you will use it in Windows 11).

Example:
\\SERVERNAME\Katlego Share

6. Create subfolders

Inside Katlego Share, create:

Sales

Marketing

Inside each folder:

Sales → create file: Sales Document.txt

Marketing → create file: Marketing Document.txt

🖥️ PART 3 — Windows 11 Testing
Log into Windows 11 as KatlegoMarketer

Open File Explorer → This PC → Network
Or use:

\\SERVERNAME\Katlego Share

You should see:

Sales

Marketing

You can open both.

Now return to Server 2022.

🛠 PART 4 — Set NTFS permissions (Server 2022)
Modify Marketing

Right-click → Properties → Security → Advanced

Click Disable inheritance

Select Convert inherited permissions into explicit permissions

Remove: Everyone

Add → Marketing group

Give Full Control

Modify Sales

Repeat steps:

Disable inheritance

Convert

Remove Everyone

Add → Sales group

Full Control

🖥️ PART 5 — Test access again (Windows 11)
Log in as Phil Marketer

Try to open:

Marketing → ✔ Should work

Sales → ❌ Should show Access Denied or you won’t see it

Screenshot 9

Take a screenshot showing:

Logged in as Phil Marketer

Access to Marketing

No access to Sales OR an Access Denied popup

Log in as Kelly Salesbody

Try to open:

Sales → ✔ Should work

Cannot see or access Katlego Marketing

Screenshot 10

Screenshot showing:

Logged in as Kelly Salesbody

Opening ales folder successfully

Log in as Ryan

Boss is part of the Manager group → can access both folders.

Screenshot 11a

Logged in as Ryan

Opening Marketing

Screenshot 11b

Logged in as Ryan

Opening Sales
