# Creating an Automation for Onboarding

# Tools Used:

--Gmail - to send out the emails to clients
--Discord - possibly to prompt things at first
--Fillout Forms - for the form
--Google Calendar - for the onboarding/kickoff and training call
--Retool - for the actualy automation

# Rules to build this out:

--Let's just go step by step, no overviews its too much to read
--Have to use Retool for the automation
--We will need to test only right now, we cannot go live until this is approved

# Drafted Process

Step 1: I need the automation to check in discord for a message containing the following information:

1. Client Name
2. Company/Legal Entity Name
3. (Optional) Additional Company/Legal Entity Name
4. Client Email address
5. If they have signed the MSA (Y/N)

Step 2: Once checked, I need an email to push out to the client, I have the email template already pre-built but may need some edits, however we need to account for the possibility of 2+ company names. Not Brands, but company/legal entity names. Also include and MSA verification because if that's not signed I need to send out an email template.

Step 3: Once that email is sent out I need the automation to check daily for if a form was filled in and/or if the client booked an onboarding call (45 min call)

Step 4: If the system detects the onboarding call was scheduled but the form wasn't filled in I need it to send an email to remind them to fill in the form before the call. I do not have a template for this.

Step 5: If they've filled in the form I need to send them a reminder to book an onboarding call with us to review the information from the form. I do not have a template for this.

Step 6: After 2 days of not receiving the form or the calendar booking, I need to push a general reminder to do both actions, I have a template for this.

Step 7: Once the form has a response(s) and an onboarding has been booked I need the client to receive a thank you and confirmation email. I do not have a template for this.

Step 8: Once the form has been received and filled in as best as possible, I need it to be checked against a checklist so I know what housekeeping to bring up top of mind in the meeting, and I'll need an agenda generated from that.

Step 9: Once the onboarding call has been completed I need to send out a recap of the call and the notes. In that recap, I want to do a pre-build confirmation where it pulls in some specific data, I have a template for this.

Step 10: I may need to manually kick this off in discord. Once I do so I'll be telling the automation that I've created their account and channels on the back end (I have a template for this email) to basically tell them they are live. Within that email I'll also need to send out training call date so that way I can get everyone logged in and using the software.

Step 11: Weekly checkin with the client via email and potentially a call. I have a template for this.

Step 12: At the 30 day mark send out another email asking them if they'd like to review anything via a call, I have a template for this.
