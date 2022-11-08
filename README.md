# StuWebARM
## Background
Provide a temporary web hosting solution for teaching basic HTML editing

## Goal
1. Create an environment that is publicly available
2. Students are able to create a CNAME in whatever domain they own to point to the site
3. Avoid creating a VM
4. Fully automated
5. Students able to upload content easily
6. Student's content is kept separate from each other

## Options
### Option #1 Static Web Site using only Storage Account Blob Storage. 

Would require a storage account per student because a storage account cannot accommodate more than one site

Pros
- Simplest design

Cons
- Requires an additional Graph API call to enable static website
- No "simple" way to get files into blob container (e.g. no SFTP option)

### Option #2 Storage Account static website with docker container with SFTP installed. 

Container users blob container in storage account to map volume that is used by SFTP

Pros
- Allows students to SFTP files to server

Cons
- URL for SFTP and web site will be different (i.e. one for the SFTP container and one for the storage account)
- Requires an additional Graph API call to enable static website
- Not sure if the container can use blob storage as a mapped volume


### Option #3 Single Storage Account with docker container with SFTP and web server installed. 
Container users file share in storage account to map volume that is used by SFTP and web server

Pros
- Allows students to SFTP files to server
- SFTP and web address are identical

Cons
- Probably the most complex
- Will likely require creating and managing custom container image

## Concept
This solution uses two containers and a Azure Storage Account per student. One container provides the SFTP functionality to allow the student to upload content and the other hosts the web server to serve up the static content. Both containers map to the same Storage Account file share. The entire solution is deployed from a single ARM template.

## Results
While it does accomplish the goals, I'm not sure that it will be cheaper than a small VM. However, it does seem to offer better automation. 

Putting SFTP and the web servier in the same container may improve cost, but then the cotainer will start to get larger and require the creation and management of a custom image. Likewise, a single container for all the SFTP would reduce cost, but would also require a custom image that has to be maintained in addition to complicating the automation. 

If there is a way to SFTP the contents to the $Web blob, the web server container could be eliminated entirely. SFTP on the storage account itself can only upload to the fileshare and it appears that the SFTP container can only map storage to the fileshare. There is still the issue of an extra Graph API call that has to be made to enable Static Website on the storage account. 
