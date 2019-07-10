# nextgen-ansible
Overview

- This is an internal explanation
	- I’d like to envision an evolution of the Ansible Tower Workshop. One that leverages storytelling and showmanship, focusing only on selling the virtues and simplicity of Ansible and Tower. 
 	- The question I have asked myself is: “If I am on the sales floor to sell you a Maserati, would I even discuss how you would change the oil, rotate the tires or install the upgraded stereo?” If you’re buying a Maserati you’ve got people do that for you. What you want to buy is the Maserati and its performance.  
     - What we have today is a very effective workshop for Open Source (Ansible) Evangelism and it’s great for the Community. However, I’ve seen that the Community types rarely write the checks. The ones who write the checks are the same ones driving the Maseratis.

As an example a director who recently hosted an Ansible Workshop provided the following feedback. He doesn't need to know how to install Ansible, Tower or a license, he just wanted to see it work. As presenters, we must strive to grab and maintain our customers’ attention from start to finish. Just one small diversion, such as having all of the participants go to email to get their licenses, exposes them to the temptation to “just glance” at the other things in the inbox and nothing good for the workshop can ever come from that. If the student is a Community advocate he or she will be fairly easy to get back on track. However, someone who is able to write the check will likely have pressing business issues jump out of the inbox at them and any presenter could have a struggle getting them fully focussed on Tower after that. Put another way, the presenter is playing the part of the magician and it’s really important that he or she can control which hand the audience is watching.
 
Environment
- RHEL Desktop with an exposed console so that all operations can go over https:
  - This would solve our ssh (and really any other) port blocking problem with the possible exception of a self signed cert.
  - Terminal access will be done via ssh or via the terminal/console capability of Cockpit
- Documentation will be kept in git and the repo will be precloned into the lab
- Five RHEL Server VMs
  - server0 to be used as the Tower
	- Tower preinstalled and pre-licensed (potentially enabled by a systemctl runonce that could pull a limited use, short lived and periodically updated licence key via authenticated curl or some other mechanism from a secure location and install it)
  - server1 to be used to host ha-proxy (or F5 in version 2)
  - server2 and server3 to be webservers
  - server4 to be a database server

- RHEL Desktop should include
	- vim pre-configured with “autocmd FileType yaml setlocal ai ts=2 sw=2 et” so that vim users will have an easier time with yaml formatting 
	- Other appropriate WYSIWYG auto-formatting editor(s) TBD
	- MSFT's Visual Code Studio https://code.visualstudio.com/ should definitely be considered

- Tower should have a basic inventory and users pre-populated (a future version might pull a dynamic inventory from Satellite and users from IDM but I don’t want to boil the ocean here)

- Future Enhancements
	- Windows VM should be added in a future release with an exposed console so that Windows users will be more at home and focussed on the course content and less on their unfamiliarity with a Linux desktop, I’m there to sell Tower to the masses, not RHEL Desktop to a Windows person. We could also use this as a target for some simple Windows automation such as installing OpenJDK which I actually do want to sell to a Windows person
	- F5 or similar virtual loadbalancer appliance to replace server1 above which would greatly enhance the appeal to Network admins. I can make the pitch using ha-proxy but seeing an appliance they are far more likely to be using will reduce the distraction and doubt and get them focussed on the value.

- “Tower can do complicated things quickly, efficiently and accurately saving you money, letting your high value workers concentrate on what makes them valuable and helps keep your customers satsified.”
  - Part of the telling is showing them the magic, demonstrate building out a two tier application with redundant web servers and a load balancer using a workflow, possibly a fast forward video to save time and keep interest.
  - Tell them about Ansible and begin their path to the DevOps side with hands on role playing:

Proposed Workshop Flow
- Introduction of Red Hat Team and Customers
- Introduction of Red Hat Ansible and Tower
- Live run of the final workflow in an instructor's environment to show the magic up front and capture attention
- Part 1
	- Walk the attendees through role playing scenarios with detailed instructions as well as options for simply copy/paste from the docs or even just a "cheat switch" to copy over a completed version of the playbook and load it into an editor for a read through if an attendee just wants to audit the course
		- “You are a sysadmin and need to install software on new servers to deliver a database server and two web servers for a new project. Here is a simple playbook that you can type in yourself or copy/paste into your editor in this particular directory and save using this filename. Once done, run run this playbook using this command.”
		- “You are a DBA and must configure a database server recently set up by your sysadmin peer. Here is a simple play book that you can enter yourself or copy/paste into your editor in this particular directory and save using this filename. Once done, run this playbook from the command line.”
		- “You are a web admin and must instantiate an application makes a call to the database server your buddy configured above. Here is a simple play book that you can enter yourself or copy/paste into your editor in this particular directory and save using this filename. Once done, run this playbook from the command line.”
		- “You are a Network Admin and must configure a loadbalancer (either server1 or the appliance) to support the efforts of your peers. Here is a simple play book that you can enter yourself or copy/paste into your editor in this particular directory and save using this filename. Once done, run this playbook from the command line.”
		- “You are a QA Analyst and must test the application. Enter this URL into the browser on your virtual desktop (Windows or Linux). That’s great but you just heard that your peers all used some automation and that you can automate your testing. Here is a simple play book that you can enter yourself or copy/paste into your editor in this particular directory and save using this filename. Once done, run this playbook from the command line.”
- Part 2
	- Wipe all servers clean with an Ansible Playbook
	- You are the Operations Engineer on duty and have gotten the call at 2am that the application environment above has been wiped out by a random maniac posing as a Solution Architect. The servers have been reprovisioned and you are to execute a workflow to re-instantiate the application automatically. Your documentation explains that the workflow will go through all of the steps used to build the environment and put it back as it was without having to wake the experts who created the automation. Having been to Tower Workshop you are confident and know that you will be successful because there will be no typos and that you will be protected because Tower will log all activity and show that you complied with policy. Make it so!
	- You are one of the Admins in charge of Tower and the Ops Engineer calls with a concern.  He has been assigned to update webservers’ OS as well as the application. This is changing something that’s not broken instead of fixing something that is and he has asked for a walk through to be sure he understands what he’s doing this first time. As an Operator he does not have the ability to edit or really even view much under the covers but you do. We’ll walk through the a workflow with plays which will:
		- Validate that the application is working properly by hitting the IP of the loadbalancer and confirming that it is performing a roundrobin of server2 and server3 (server2 and server3 will serve up essentially the same content but will be differentiated by an identifier in one corner showing the hostname as instantiated via a jinja2 template deployed from the web admin’s playbook)
		- Take server2 out of the loadbalancer
		- Validate that traffic is still flowing properly through the loadbalancer from server3
		- Update the OS and the application on server2
		- Reboot server2
		- Validate that server2 comes up, that we can connect to it via ssh and that it serves up proper content from the webserver
		- Place server2 back into the loadbalancer
		- Validate that traffic is still flowing properly through the loadbalancer
		- Take server3 out of the loadbalancer
		- Validate that traffic is still flowing properly through the loadbalancer from server2
		- Update the OS and the application on server3
		- Reboot server3
		- Validate that server3 comes up, that we can connect to it via ssh and that it serves up proper content from the webserver
		- Place server3 back into the loadbalancer
        - Validate that traffic is still flowing properly through the loadbalancer per its roundrobin configuration
	- “As your now more confident Ops Persona you execute the workflow above and verify success in the Tower UI”
	- “You are the Director of your division (or perhaps an auditor) and want to make sure that the update you saw on the schedule for today was a success. You log into Tower and review the job history.”

- (Can we fit this into the story line above and still be within a reasonabletime limit?) You are a system admin and have been tasked with discovering/verifying specific facts across the organization.

- Tell them what you told them “Ansible Tower will change your life!” Discuss the power of Ansible and the force multiplier nature of Tower, Q&A, walk through the ROI Calculator with all of the participants, define next steps (PoC, PO, Services Engagement, Training).
