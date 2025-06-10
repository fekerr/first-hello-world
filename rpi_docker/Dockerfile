# Base image - Changed to arm32v7/debian for testing Docker Hub access
FROM arm32v7/debian:bullseye-slim

# Install SSH server and other useful tools
RUN apt-get update && apt-get install -y --no-install-recommends     openssh-server     sudo     nano     curl     net-tools     apt-utils     && rm -rf /var/lib/apt/lists/*

# Create a directory for SSH daemon
RUN mkdir -p /var/run/sshd

# Configure SSH
# Permit root login with password (consider changing to key-based auth for better security)
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config || echo "PermitRootLogin line not found or failed to update"
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config || echo "PermitRootLogin direct update failed"
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config || echo "PasswordAuthentication line not found or failed to update"
RUN sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config || echo "PasswordAuthentication direct update to yes failed"

# Ensure UsePAM is set correctly, default might be 'no' in some base images
# Debian default is 'UsePAM yes'
RUN if grep -q "^UsePAM" /etc/ssh/sshd_config; then         sed -i 's/UsePAM .*/UsePAM yes/' /etc/ssh/sshd_config;     else         echo "UsePAM yes" >> /etc/ssh/sshd_config;     fi

# Set a default root password (CHANGE THIS IN ACTUAL USE)
RUN echo 'root:raspberry' | chpasswd

# Create a standard user 'pi' with password 'raspberry' and sudo access
RUN useradd -ms /bin/bash pi &&     echo 'pi:raspberry' | chpasswd &&     adduser pi sudo

# Expose SSH port
EXPOSE 22

# Start SSH daemon
CMD ["/usr/sbin/sshd", "-D"]
