# PBL5-Darey.io
Aux Project 1_Shell Scripting

## PROJECT SCOPE
In this project, you need to onboard 10 new Linux users onto a server. Create a shell script that reads a csv file that contains the first name of the users to be onboarded.

You have been learning about Linux for some time, and it is time to start getting a feel of how to automate some work using Shell Scripts.

## SO WHAT IS SHELL SCRIPTING ?
A shell script is a text file that contains a sequence of commands for a UNIX-based operating system. It is called a shell script because it combines a sequence of commands, that would otherwise have to be typed into the keyboard one at a time, into a single script. The shell is the operating system's command-line interface (CLI) and interpreter for the set of commands that are used to communicate with the system.

A shell script is usually created for command sequences in which a user has a need to use repeatedly in order to save time. Like other programs, the shell script can contain parameters, comments and subcommands that the shell must follow. Users initiate the sequence of commands in the shell script by simply entering the file name on a command line.

The basic steps involved with shell scripting are writing the script, making the script accessible to the shell and giving the shell execute permission.

find out more - https://www.techtarget.com/searchdatacenter/definition/shell-script


### STEP 1 
Create the project folder called Shell
*mkdir Shell*

### STEP 2 
Create a csv file name names.csv
*touch names.csv*

Open the names.csv file, Insert some random names into it. (One name per line)
*vim names.csv*

### STEP 3 - 7
The script you created should read the CSV file, create each user on the server, and add to an existing group called developers (You will need to manually create this group ahead).

Ensure that your script will first check for the existence of the user on the system, before it will attempt to create that it.

Ensure that the user that is being created also has a default home folder

Ensure that each user has a .ssh folder within its HOME folder. If it does not exist, then create it.

For each user’s SSH configuration, create an authorized_keys file and add ensxure it has the public key of your current user.

Using VS Code, below shell script was created to capture all requirements above...more detail explaination is on https://www.youtube.com/watch?v=NysMJ9rhVIU

#### Shell Scripting
#### This Script will read a CSV file that contains 20 new Linux users.
#### This script will create each user on the server and add to an existing group called 'Developers'.
#### This script will first check for the existence of the user on the system, before it will attempt to create
#### The user that is being created must also have a default home folder
#### Each user should have a .ssh folder within its HOME folder. If it does not exist, then it will be created
#### For each user's SSH configuration, we will create an authorized key file and add the below public key

#### !/bin/bash
userfile=$(cat names.csv)
PASSWORD=password

#### To ensure user running this script has sudo priviledge
    if [ $(id -u) -eq 0 ]; then

#### Reading the CSV file
        for user in $userfile;
        do
            echo $user
        if id "$user" &>/dev/null
        then
            echo "User Exist"
        else
        
#### This will create a new user
        useradd -m -d /home/$user -s /bin/bash -g developers $user
        echo "New User Created"
        echo
        
        
#### This will create an ssh folder in the user home folder
        su - -c "mkdir ~/.ssh" $user
        echo ".ssh directory created for new user"
        echo

#### We need to set the user permission for the ssh dir
         su - -c "chmod 700 ~/.ssh" $user
         echo "user permission for .ssh directory set"
         echo

#### This will create an authorized-key file
        su - -c "touch ~/.ssh/authorized_keys" $user
        echo "Authorized key File Created"
        echo

#### We need to set permission for the key file
        su - -c "chmod 600 ~/.ssh/authorized_keys" $user
        echo "user permission for the Authorized key file set"
        echo

#### We need to create and set public key for the users in the server
        cp -R "/root/onboard/id_rsa.pub" "/home/$user/.ssh/authorized_keys"
        echo "Copyied the Public Key to New User Account on the server"
        echo
        echo

        echo "USER CREATED"

#### Generate a password.
sudo echo -e "$PASSWORD\n$PASSWORD" | sudo passwd "$user"
sudo passwd -x 5 $user
            fi
        done
    else
    echo "Only Admin Can Onboard A User"
    fi



Prepare the Shell Script using VS Code, save the file in same folder as you .pem file. Run below CMD to move the Shell Script(saved as onboard.sh) from your laptop to your Ubuntu server

*scp -i EZE_PBL5.pem onboard.sh ubuntu@3.238.218.70:~/;*

Before Deploying your script, you will need to create files where you will update you public and private keys - *touch id_rsa id_rsa.pub*
Now update your current user with the correct public key and private key, using *vi* to update the files, also use *vi* to add the name of the users you want to onboard

![PBL5_3](https://user-images.githubusercontent.com/122687798/222316394-1b77c09b-341f-4136-9e47-96da13fd3552.JPG)

*See marked red and the comments*

![PBL5_4](https://user-images.githubusercontent.com/122687798/222321048-5697c49c-cb76-421f-b321-8497aa6cb2fb.jpg)

