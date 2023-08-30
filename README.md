# ISORevenue
import os
import zipfile
from ftplib import FTP

# FTP server credentials
ftp_host = 'ftp.example.com'
ftp_user = 'username'
ftp_pass = 'password'

# Remote folder containing files to download
remote_folder = '/remote/folder/'

# Local folder to download files to
local_folder = '/local/folder/'

# Shared folder to move unzipped files to
shared_folder = '/shared/folder/'

# Connect to FTP server
ftp = FTP(ftp_host)
ftp.login(user=ftp_user, passwd=ftp_pass)

# Change to the remote folder
ftp.cwd(remote_folder)

# Get list of files in remote folder
file_list = ftp.nlst()

# Download each file to local folder
for file_name in file_list:
    local_file_path = os.path.join(local_folder, file_name)
    with open(local_file_path, 'wb') as local_file:
        ftp.retrbinary('RETR ' + file_name, local_file.write)

    # Unzip the file
    with zipfile.ZipFile(local_file_path, 'r') as zip_ref:
        zip_ref.extractall(local_folder)

    # Move unzipped files to shared folder
    unzipped_folder = os.path.splitext(local_file_path)[0]
    for unzipped_file_name in os.listdir(unzipped_folder):
        unzipped_file_path = os.path.join(unzipped_folder, unzipped_file_name)
        shared_file_path = os.path.join(shared_folder, unzipped_file_name)
        os.rename(unzipped_file_path, shared_file_path)

# Disconnect from FTP server
ftp.quit()
