# Ansible Galaxy
Proyecto de administración de servicios de AWS con ansible, se crearan los sieguientes recursos:

- VPC
- IGW
- Subnet
- Route table
- Security group
- Instancia en EC2

## Configuración de el CLI de AWS
Establecer los datos usuario en el CLI de AWS

```
aws configure
```

Variables a ingresar:

- AWS Access Key ID="ID_de_usuario"
- AWS Secret Access Key="Llave_secreta_de_acceso"
- Default region name=""
- Default output format=""

## Creacion de archivo host  para Ansible

Crear archivo un archivo llamado hosts-aws e ingresar los siguientes datos:

```
[local]
localhost ansible_connection=local ansible_python_interpreter=python
```

## Creacion de Playbook
Crear un archivo YAML para almacenar las configuraciones. El archivo debera contener lo siguiente al inicio del archivo:
```
---
- name: Provision an EC2 Instance
  hosts: local
  connection: local
  gather_facts: False
  tags: provisioning

```
Todas las tareas que se deseen ejecutar, deberan estar contenidas dentro del bloque task
## Creación de VPC  en ansible

Para la creación de las VPC, el cual se debera ver de la siguiente forma:

```
    - name: Create a VPC
      ec2_vpc_net:
        name: "vpc_name"
        cidr_block: "cidr"
        region: "region"
```

## Creación del Internet gateway
```
    - name: Create Internet Gateway (IGW)
      ec2_vpc_igw:
        region: "region"
        vpc_id: "vpc_id"
```

## Subnet
```
    - name: Create new Subnet
      ec2_vpc_subnet:
        cidr: "cdir_subnet"
        state: present
        region: "region"
        vpc_id: "vpc_id"
        map_public: yes
```

## Route table
```
    - name: Set up public subnet route table
      ec2_vpc_route_table:
        vpc_id: "vpc_id"
        region: "region"
        subnets:
          - "subnet_id"
        routes:
          - dest: 0.0.0.0/0
            gateway_id: "gateway_id"
```

## Security group
```
    - name: Create a security group
      ec2_group:
        name: "security_group"
        region: "region"
        vpc_id: "vpc_id"
        rules:
          - proto: tcp
            from_port: 22
            to_port: 22
            cidr_ip: 0.0.0.0/0
        rules_egress:
          - proto: all
            cidr_ip: 0.0.0.0/0
```

## Crear una instancia de EC2
```
    - name: Launching new EC2 Instance
      ec2:
        group: "security_group"
        instance_type: "instance_type"
        image: "image"
        wait: true
        wait_timeout: 500
        region: "region"
        keypair: "keypair"
        count: "count"
        vpc_subnet_id: "subnet_id"
```
# Reto 1

## Revisar de la instancia via ssh
```
    - name: Let's wait for SSH to come up. Usually that takes ~10 seconds
      local_action: wait_for
        host=public_ip
        port=22
        state=started
      with_items: "{{ ec2_ansible.instances }}"
```

## Guardar DNS público de la instancia 

```
    - name: Add the newly created EC2 instance(s) to the new local host group
      local_action: lineinfile
```

## Agregar TAG a una instancia
```
    - name: Add TAG to Instance(s)
      local_action:
  ```
  
# Reto 2

- Crear un solo archivo que pueda crear de forma consecuita todos los elementos anteriores
