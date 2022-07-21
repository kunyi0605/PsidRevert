
target := /dev/targetDevice
PSID := "INSERTPSID"
logfile := running_log.txt
#target := $(shell sudo sedutil-cli --scan | grep SATA | cut -c1-8)

all: loop

loop:
	if echo ${target} | grep "/dev" ; then echo found SMART drive at ${target} ; else exit 2 ; fi
	sudo systemctl stop udisks2.service 	
	number=1; while [ $$number -le loopcount ]; do\
	    make psidresetwithheader >> ${logfile} 2>&1; \
	    echo $$number; \
	    number=$$((number+1)); \
	    sleep 1; \
	done
            #sudo hdparm -I /dev/targetDevice | grep -i speed;\
            #dmesg | grep -i sata | grep 'link up';\
            #sudo hdparm -tT /dev/targetDevice;\
            #dd if=/dev/zero of=/tmp/testALT.img bs=1G count=1 conv=fdatasync;\
            #rm /tmp/testALT.img;\
            #dd if=/dev/zero of=/tmp/output.img bs=8k count=256k;\
            #rm /tmp/output.img;\

psidresetwithheader:
	    date
	    make psidreset
	    echo 
	    echo ====================================================================
	    echo

init:
	sudo mkdir /mnt || true
	sudo mount -t tmpfs -o size=2m tmpfs /mnt
	dd if=/dev/zero of=EOM.img bs=1M count=16
	mkfs -t fat EOM.img
	sudo mkdir /mnt/EOM
	sudo mount -t auto -o loop EOM.img /mnt/EOM/
	sudo mkdir /mnt/EOM/EFI
	sudo mkdir /mnt/EOM/EFI/BOOT
	#sudo cp --preserve=timestamps FILES/EFI/BOOT/BOOTX64.EFI /mnt/EOM/EFI/BOOT/
	#sudo cp --preserve=timestamps FILES/STARTUP.NSH /mnt/EOM/
	#sudo cp --preserve=timestamps FILES/*.efi /mnt/EOM/
	sudo rsync -avh /boot/efi/* /mnt/EOM
	sudo umount /mnt/EOM
	sudo umount /mnt

EOM.img: 
	make init

psidreset: EOM.img
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli --PSIDrevert ${PSID} ${target} 
	sudo dd   bs=4M  if=EOM.img   of=${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --initialsetup         debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --enablelockingrange 0 debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --setmbrdone on        debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --setmbrenable off     debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --loadPBAimage         debug /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/UBUNTU/EOM.img ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --setlockingrange 0 RO debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --setmbrdone on        debug         ${target}
	cd /home/smartm_user/Jenkins/TestItem/Linux-Item/PsidRevert/sedutil && sudo ./sedutil-cli -n --setmbrenable on      debug         ${target}

test: EOM.img
	echo Hello


#	sudo ./sedutil-cli -n --initialsetup         debug         ${target}
#	sudo ./sedutil-cli -n --enablelockingrange 0 debug         ${target}
#	sudo ./sedutil-cli -n --setmbrdone on        debug         ${target}
#	sudo ./sedutil-cli -n --setmbrenable off     debug         ${target}
#	sudo ./sedutil-cli -n --loadPBAimage         debug EOM.img ${target}
#	sudo ./sedutil-cli -n --setlockingrange 0 RO debug         ${target}
#	sudo ./sedutil-cli -n --setmbrenable on      debug         ${target}
#	sudo ./umount ${target}
#
#	sudo ./sedutil-cli --PSIDrevert 5451021EDEEC43FF9808EF266A49FAB6 ${target}
#	sudo dd   bs=4M  if=EOM.img   of=${target}
#	sudo ./sedutil-cli -n --initialsetup         debug         ${target}
#	sudo ./sedutil-cli -n --enablelockingrange 0 debug         ${target}
#	sudo ./sedutil-cli -n --setmbrdone on        debug         ${target}
#	sudo ./sedutil-cli -n --setmbrenable off     debug         ${target}
#	sudo ./sedutil-cli -n --loadPBAimage         debug EOM.img ${target}
#	sudo ./sedutil-cli -n --setlockingrange 0 RO debug         ${target}
#	sudo ./sedutil-cli -n --setmbrdone on        debug         ${target}
#	sudo ./sedutil-cli -n --setmbrenable on      debug         ${target}


