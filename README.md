# Rede-Segura---Cisco-Packet-Tracer
Este projeto tem como objetivo, demonstrar o funcionamento, segurança e confiabilidade de uma rede corporativa segura no Packet Tracer. A metodologia, protocolos de comunicação e gerenciamento de segurança foi realizada a partir do que consegui extrair de conhecimento das trilhas de aprendizagem do NetAcad.

# Topologia Principal.
<img width="1032" height="698" alt="topologia" src="https://github.com/user-attachments/assets/5e5ec0c3-df0f-4a01-b6ec-ac5c36d6ed8d" />
  
O projeto foi segmentado em 6 VLans (100, 110, 120, 130, 140 e 150) alimentadas com servidores DHCP para distribuição de IP's, DNS para resolução de nomes e um servidor RADIUS agindo como AAA.  

As VLans são: Produção, RH, Financeiro e Compras. Na parte dos roteadores na Full-mesh, temos a VLan Wi-fi recreativa para funcionários, separada para preservar o interior da rede.  

A base da rede consiste em 2 Switches de Acesso, 2 Switches de Distribuição garantindo o roteamento confiável e 3 roteadores, ambos em prol de otimizar a rede, agindo com redundância assim mantendo a disponibilidade da infraestrutura.  

# VLans

**VLAN 100** (Servidores) 192.168.100.0 dedicada ao pátio de servidores.

**VLAN 110** (Produção)  192.168.110.0 dedicada ao setor administrativo da Produção.  

**VLAN 120** (RH)  192.168.120.0 dedicada ao setor de RH.  

**VLAN 130** (Recreativa)  192.168.130.0  recreativa para colaboradores, isolada para melhor segurança do interior principal da rede.  

**VLAN 140** (Financeiro)  192.168.140.0 dedicada ao setor de Financeiro.  

**VLAN 150** (Compras)  192.168.150.0 dedicada ao setor de Compras.  

# Switches de Acesso.
Os SW's foram configurados para agir como pontes entre os Hosts e o SW's de Distribuição, foram usados dois Switches Layer 2.  

**Configuração para ambos SW's.**  
Para configurar as portas como parte das Vlans, foi usado o seguinte comando:  

_interface range fa0/1 - 5_  
_switchport mode access_    <---- _Configuração aplicada em todas VLans_.  

Como ambos os Switches possuem 24 portas FastEthernet, para redução da superficie de ataques, foram desligadas todas as portas remanescentes que não estão em uso: (Exemplo no Switch).  


<img width="629" height="409" alt="shutdown interfaces" src="https://github.com/user-attachments/assets/620eb26f-852f-443e-b1bc-e4d2f00e6824" />  

Foi inserido o cmd _storm controll_ para evitar que a rede receba um ataque de broadcast, que no caso poderia ser fatal se as portas ainda estivessem em _no shutdown_

# Switches de Distribuição.  
Sendo eles Switches multilayer, nas interfaces GigabitEthernet do SW's de Acesso e Roteadores foram inseridos os seguintes comandos:  

 _switchport mode trunk_  
 _switchport trunk allowed vlans 110,120,130,140,150_   
 
 _Algumas Vlans foram separadas neste processo, para comunicação com seus respectivos SW's de acesso_.  

E também, ajustados os valores para cada Vlan, em prol da configuração de endereçamento dinâmico:  

**vlan 110**

 ip address 192.168.110.1 255.255.255.0  
 ip helper-address 192.168.100.5     <----   IP Server DHCP  

*Foi configurado em todas as Vlans, com seus respectivos endereços (também foi replicado o comando shutdown nas portas sem uso*  

# Roteadores.
Os Roteadores foram configurados sub-interfaces para as VLans:  

 _int gigabitethernet0/0/1.120_  
 _ip address 192.168.120.1 255.255.255.0_  
 _ip helper-address 192.168.100.5_  
 _ip route 192.168.100.0 Serial0/1/1_  

_Também foi configurado na full-mesh e nos switches, os IP's das VLANs cadastradas com o protocolo_ **RIP Routing** _para maior flexibilidade de roteamento_:  

30.30.30.10  
30.30.30.20  
40.40.40.10  
40.40.40.20  
50.50.50.10  
50.50.50.20  

**Para permitir acesso remoto seguro aos roteadores/switches via SSH (e desabilitar o Telnet inseguro) foi aplicado o seguinte comando:**  

<img width="517" height="128" alt="cryptokey R1" src="https://github.com/user-attachments/assets/6d5cb326-9ab7-441b-a747-9e97d0a876e3" />



# DHCP
O servidor foi configurado para fornecer DHCP dinâmico para os hosts das VLans;  

<img width="506" height="551" alt="DHCP" src="https://github.com/user-attachments/assets/2bfe8307-db95-4087-855d-89e26682f4e6" />

_Em seguida, Host com endereço atribuido:_  

<img width="657" height="167" alt="DHCP-PC" src="https://github.com/user-attachments/assets/6beb565a-49f4-43ad-b646-45c176f2f149" />

# DNS
Foi configurado o DNS para tradução de nomes para as Hosts, e foi criado um test server que é o _www.test.com_:

<img width="528" height="549" alt="DNS" src="https://github.com/user-attachments/assets/8ea4900d-dc74-4825-ab67-7cdef8d60926" />  

Foi testado a conectividade, usando o cmd _ping_ para checar a tradução de nomes:

<img width="403" height="196" alt="ping-teste-dns" src="https://github.com/user-attachments/assets/03188fdf-4b47-457b-af70-8e0b31546b71" />

# Radius
Foi configurado um servidor AAA para autenticação, autorização e contabilidade, com duas contas genéricas criadas para servir de exemplo:

<img width="503" height="615" alt="AAA" src="https://github.com/user-attachments/assets/5f44ca70-0ef3-4a41-bd0f-2eb446596ee8" />4  

# Práticas de Segurança aplicadas.
Elaborei e apliquei diversos métodos de segurança, além dos que mencionei acima. Para segurança da infraestrutura, a fim de manter disponibilidade, integridade e confidencialidade.  

# DHCP Snooping
Nas interfaces de acesso, defini o DHCP trust entre as VLANS para o servidor real, evitando entrega de IP's maliciosos para os hosts da Infraestrutura como das portas conectadas de PC's ou Impressoras, segue exemplo:  

<img width="389" height="73" alt="dhcp snooping" src="https://github.com/user-attachments/assets/5bfa197c-ceed-4745-986f-4109c0519a0b" />

# ARP Inspection
Foi configurado também o ARP Inspection para evitar ARP Spoofing entre as VLANS, assim evitando prévias negações de Serviço.

<img width="398" height="45" alt="arp inspection" src="https://github.com/user-attachments/assets/f7ecab42-6995-4b19-8905-f64d17431316" />

# CMDS para ataques comuns.
_no cdp run_      <-- para de enviar/receber pacotes cdp para os hosts.  

_no lldp run_     <-- a mesma coisa que o cdp, só que para o link layer discover protocol.  

_no ip domain-lookup_  <-- desativa a resolução DNS ao digitar um comando errado.  

# Banner de login e password encryption.

Foi aplicado um Banner de login para alerta, e em seguida o password para acesso de configurações do hardware:

<img width="449" height="177" alt="banner+senha" src="https://github.com/user-attachments/assets/e83aebc9-b279-464d-a209-a6828fbeb7f5" />  


# Passwords no usermode e globalmode.
E para finalizar, na infraestrutura padrão também foi definido dois tipos de passwords para os Hardenings que são;

_usermode: emp.generica123_  
_globalmode: admin.generico1234_  

Como um adicional, também foi adicionado o password encrypt, para as senhas.

# Agradeço o tempo de leitura!!!

Gostaria de dizer que foi demorado, mas consegui finalmente terminar meu projeto, é gratificante ver algo que você começou do 0 completo, estou aberto a sugestões! Obrigado :)






