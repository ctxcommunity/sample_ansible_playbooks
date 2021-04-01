

::Configure WinRM
cmd.exe /c winrm quickconfig -q
cmd.exe /c netsh advfirewall firewall add rule name= "Windows Remote Management (HTTPS-In)" dir=in action=allow protocol=TCP localport=5986
cmd.exe /c net stop winrm
cmd.exe /c net start winrm

::Create Self-Signed Certificate and Bind to WinRM
$cert = New-SelfSignedCertificate -DnsName win10.theshonkproject.com -CertStoreLocation Cert:\LocalMachine\My -NotAfter (get-date).AddYears(5)

$selector_set = @{
       Address = "*"
       Transport = "HTTPS"
}
$value_set = @{
       CertificateThumbprint = $cert.ThumbPrint
}

New-WSManInstance -ResourceURI "winrm/config/Listener" -SelectorSet $selector_set -ValueSet $value_set


Ansible host file needs to be configured

Need to specify collections location
/etc/ansible/ansible.cfg
  collections_paths = ./collections



Download Roles:
ansible-galaxy collection install ctxcommunity.cvad
ansible-galaxy collection install ctxcommunity.wem
ansible-galaxy collection install ctxcommunity.pvs



ansible-playbook playbook --extra-vars="runas_admin={Admin}"

{Admin} is the name .yml file configured in the pw_vars directory

Example:
  ansible-playbook install_cvad_storefront_server.yml --extra-vars="runas_admin=joe"
