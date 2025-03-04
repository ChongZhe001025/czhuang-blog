<h3 id="a-few-things-you-should-know">
    <strong>1. Shut down the VM and create a serial port using the qm command on the Host</strong>
</h3>
<blockquote>
    qm set target-vm-id -serial0 socket
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>2. Start the VM and use dmesg to check if ttyS appears</strong>
</h3>
<blockquote>
    sudo dmesg | grep ttyS
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>3. Enter edit mode</strong>
</h3>
<blockquote>
    sudo nano /etc/default/grub
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>4. Modify parameters</strong>
</h3>
<blockquote>
    GRUB_CMDLINE_LINUX="quiet console=tty0 console=ttyS0,115200"
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>5. Update grub</strong>
</h3>
<blockquote>
    sudo update-grub
</blockquote>

<h3 id="a-few-things-you-should-know">
    <strong>6. Reboot</strong>
</h3>
<blockquote>
    sudo reboot
</blockquote>

<!-- <h3 id="a-few-things-you-should-know">
    <strong>For Fedora</strong>
</h3>
<blockquote>
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
</blockquote> -->
