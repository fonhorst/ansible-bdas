How

up 3 vm:
/ansible-bdas/ vagrant up

up 1 vm:
/ansible-bdas/controlmachine-vm vagrant up

connect to controlmachine vm:
/ansible-bdas/controlmachine-vm vagrant ssh controlmachine

inside controlmachine vm:
cd ~/project/ansible-bdas

deploy mesos:
ansible-playbook mesos.yml

deploy crawler:
ansible-playbook crawler --tags="prepare,build"

run crawler:
ansible-playbook crawler --tags="start_mesos_mode"

see master log:
ansible master -a "cat /SNCrawler/output/MasterActor.txt"

see workers log:
http://192.168.92.11:5050/#/frameworks

stop crawler:
ansible-playbook crawler --tags="stop"


