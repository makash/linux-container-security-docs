### What is LSM?

The Linux Security Module (LSM) framework provides a mechanism for
various security checks to be hooked by new kernel extensions.

The primary users of the LSM interface are Mandatory Access Control
(MAC) extensions which provide a comprehensive security policy. Examples
include SELinux, Smack, Tomoyo, and AppArmor.