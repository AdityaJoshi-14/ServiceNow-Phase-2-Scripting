**** 30/05/2026 ***** 

Data Policy


# Get the below incident and close it
INC0000015



Make close info mandatory when resolved or closed


var gr = new GlideRecord('incident');
gr.addQuery('number', 'INC0000015');
gr.query();
if(gr.next())
{
gr.state = 6; // resolved
gr.close_code = 'successful';
gr.close_notes  = "closing for datapolicy class";
gr.update();
}


# Table: asset
condition = closed/cancelled
closure notes - mandatory

Q: Can every ui policy coverted to DataPolicy?

#1: Global = checked
#2: Visible = Leave alone
#3: Run scripts = unchecked


Difference between UI and data policy
- client vs server (database) level, (data policy can work both sides)
- no visible in data policy
-  clear value
- UI Policy Related List Actions avaialble in ui policy only



problem - child incidents

Rule 1: dont allow to cancle any problem without closure notes
Rule 2: dont allow to cancle any incident without  closure notes


Assignment:  
    - practice of data policy

