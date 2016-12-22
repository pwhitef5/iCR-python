# iCR-python
v1.1 22nd December 2016
iControl REST module

a Python module to simplify use of the F5 iControl REST interface

uses requests and json modules

Install using `pip install iCR`

Methods:
- init(hostname,username,password,[timeout,port,icontrol_version,folder,token,debug])
- get(url,[select,top,skip])
- create(url,data)
- modify(url,data,[patch=True])
- delete(url)

Module Variables:

- icr_session - the link to the requests session
- raw - the raw returned JSON
- code - the returned HTTP Status Code eg 200
- error - in the case of error, the exception error string
- headers - the response headers

Features: iControl version, debug mode, folders, iWorkflow tokens, select, top, skip

Example:

   ```
   #!/usr/bin/env python
   from iCR import iCR
   bigip = iCR("172.24.9.132","admin","admin",icontrol_version="11.6.0",debug=False)
   # List all Virtual Servers in Common partition
   virts = bigip.get("ltm/virtual")
   # Write a list of the Virtual Servers
   for vs in virts['items']:
      print vs['name']
   print "+++++++++++++++++"
   print

   #Create a Virtual Server
   vs_config = {'name':'test_vs'}
   createvs = bigip.create("ltm/virtual",vs_config)

   # Retrieve the VS we just created
   virt = bigip.get("ltm/virtual/test_vs",select="name")
   print "Virtual Server created: " + virt['name']

   # Now delete the VS we just created
   delvs = bigip.delete("ltm/virtual/test_vs")

   # Retrieve ASM policy to ID mapping
   policies = bigip.get("asm/policies",select="name,id")
   # Print  a table of ASM policies with learning mode
   print
   print "Policy Name                  Learning Mode"
   print "------------------------------------------"
   for item in policies['items']:
      enabled = bigip.get("asm/policies/" + item['id'] + "/policy-builder",select="learningMode")
      print '{:32}'.format(item['name']) + enabled['learningMode']
   ```
    
This gives the output:

   ```
   $ ./test.py
   VS_test
   VS_ASM_WebApp2
   VS_ASM_WebApp2_Redirect
   VS_DNS
   test_mgmt_vs
   +++++++++++++++++

   Virtual Server created: test_vs

   Policy Name                  Learning Mode
   ------------------------------------------
   ASM_Policy_Web-App_1            disabled
   IDW_1                           disabled
   test                            disabled
   rdp_test                        disabled
   ```

