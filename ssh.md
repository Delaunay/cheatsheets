SSH
===

# Generate Keys

    ssh-keygen -t ed25519 -C "From home to Mila" -f ~/.ssh/mila

# Make a ssh config file

    vi ~/.ssh/config

    # Mila - Kerberos
    Host mila login-1.server.mila.quebec
    User $USER
    Hostname login-1.server.mila.quebec
    IdentityFile ~/.ssh/mila
    Port 8001
    ServerAliveInterval 60
    ServerAliveCountMax 5
    GSSAPIAuthentication yes
    GSSAPIDelegateCredentials yes
    GSSAPIClientIdentity $USER@SERVER.MILA.QUEBEC
    GSSAPIKeyExchange yes
    PreferredAuthentications gssapi-with-mic,password
    
    # Mila

    Host mila login-1.server.mila.quebec
    User $USER
    Hostname login-1.server.mila.quebec
    Port 8001
    ServerAliveInterval 60
    ServerAliveCountMax 5
    PreferredAuthentications publickey,password
    ControlMaster auto
    ControlPath ~/.ssh/control-%h-%p-%r
    ControlPersist yes
    
# Allow the remote access

    scp ~/.ssh/mila.pub mila:mila.pub
    mkdir -p .ssh
    chmod 700 .ssh
    cat mila.pub >> .ssh/authorized_keys
    chmod 600 .ssh/authorized_keys
    rm mila.pub
