Change password of default kali@kali:

    sudo passwd kali


Change username: 

(Make sure you are not logged in as the user that you want to change the name of.)

OLD_USERNAME from here onward is gonna be "kali" if you're genuinely just fresh off installation.

    sudo usermod -l NEW_USERNAME -d /home/NEW_USERNAME -m OLD_USERNAME


Change primary group's name after changing username: 

    sudo groupmod -n NEW_USERNAME OLD_USERNAME

    
Enter hosts and edit hostname:

    sudo nano /etc/hosts



