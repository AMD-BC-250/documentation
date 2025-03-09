# Optimization Tips

## Hardware vulnerabilities mitigation

Set `mitigations=off` kernel boot parameter to lower the CPU usager. 

Cyan Skillfish ucode does not contains Zenbleed fixes so mitigation could be costly.

* OpenSUSE
    * YaST -> System -> Boot Loader -> Kernel Parameters -> CPU Mitigations -> Off  
