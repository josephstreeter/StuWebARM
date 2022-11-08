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
