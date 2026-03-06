# Laboratorio de Implementación de VPNs Avanzadas

**Institución:** Instituto Tecnológico de las Américas (ITLA)  
**Estudiante:** Junior Duran 2024-2015

## Introducción
Este repositorio contiene la documentación técnica, parámetros de seguridad y scripts de configuración para diversas arquitecturas de Redes Privadas Virtuales (VPN). El laboratorio abarca desde implementaciones Site-to-Site tradicionales y basadas en rutas (VTI), hasta la encapsulación GRE y redes dinámicas multipunto (DMVPN). 

*Nota: Los scripts de configuración de cada router se encuentran disponibles como archivos de texto (.txt) en la raíz de este repositorio.*

---

## 1. VPN Site-to-Site con IKEv2 (Basada en Políticas)

### 🎯 Objetivo
Establecer un túnel seguro entre dos sucursales utilizando el protocolo IKEv2 para una negociación rápida y robusta, dependiendo de Listas de Control de Acceso (ACLs) para definir el tráfico interesante que debe ser encriptado.

### ⚙️ Parámetros Criptográficos
* **Fase 1 (IKEv2):** Encriptación AES-CBC-256, Integridad SHA-1, Grupo DH 14. Autenticación mediante Pre-Shared Keys (PSK).
* **Fase 2 (IPSec):** Transform-set ESP-AES 256 y ESP-SHA-HMAC. Operando en `mode tunnel`. Crypto Map aplicado a la interfaz física.

### 🗺️ Topología y Direccionamiento
* **Red LAN Sede 1:** 192.168.10.0/24
* **Red LAN Sede 2:** 192.168.20.0/24
* **Red WAN (ISP):** 20.24.20.0/30 (R1) y 20.24.20.4/30 (R2)

![Topología VPN 1]

<img width="358" height="251" alt="image" src="https://github.com/user-attachments/assets/4a99a212-e769-4c9b-9056-87cc6dcc4039" />



---

## 2. VPN Site-to-Site con IKEv1 (Basada en Rutas / VTI)

### 🎯 Objetivo
Simplificar el enrutamiento de la red privada virtual eliminando la dependencia de Listas de Control de Acceso (ACLs). Se implementan Interfaces de Túnel Virtuales (VTI) basadas en rutas, permitiendo que cualquier tráfico enrutado hacia la interfaz virtual sea cifrado automáticamente.

### ⚙️ Parámetros Criptográficos
* Interfaz Tunnel, enrutamiento estático apuntando a la interfaz virtual, Transform-set operando en `mode tunnel`, y Perfil IPSec aplicado directamente como protección de la interfaz (`tunnel protection ipsec profile`).

### 🗺️ Topología y Direccionamiento
* **Red Túnel Virtual (VTI):** 10.10.10.0/30
* *(LAN y WAN se mantienen igual que en el escenario anterior).*

![Topología VPN 2]
<img width="522" height="309" alt="image" src="https://github.com/user-attachments/assets/3dd8043d-1c49-42b5-834e-723e0fac666d" />


---

## 3. VPN Site-to-Site con IKEv2 (Basada en Rutas / VTI)

### 🎯 Objetivo
Simplificar el enrutamiento de la red privada virtual eliminando la dependencia de Listas de Control de Acceso (ACLs). Se implementan Interfaces de Túnel Virtuales (VTI) basadas en rutas, permitiendo que cualquier tráfico enrutado hacia la interfaz virtual sea cifrado automáticamente. (Implementación asegurada con el protocolo IKEv2).

### ⚙️ Parámetros Criptográficos
* Interfaz Tunnel, enrutamiento estático apuntando a la interfaz virtual, Transform-set operando en `mode tunnel`, y Perfil IPSec aplicado directamente como protección de la interfaz (`tunnel protection ipsec profile`). Adicionalmente, el perfil IPSec se vincula de manera explícita al perfil IKEv2.

### 🗺️ Topología y Direccionamiento
* **Red Túnel Virtual (VTI):** 10.10.10.0/30

![Topología VPN 3]
<img width="522" height="309" alt="image" src="https://github.com/user-attachments/assets/bb734d3a-0eeb-46d2-8477-dcf735d56011" />


---

## 4. GRE sobre IPSec (con IKEv1)

### 🎯 Objetivo
Encapsular tráfico de red multiprotocolo utilizando túneles GRE (Generic Routing Encapsulation) y proveer confidencialidad e integridad a dicha comunicación aplicando una capa de encriptación IPSec por encima del túnel.

### ⚙️ Parámetros Criptográficos
* Interfaz operando en `tunnel mode gre ip`. En la Fase 2 de IPSec, el Transform-set se configuró en `mode transport` en lugar de modo túnel; esto es una mejor práctica para evitar una doble encapsulación innecesaria, ya que GRE se encarga de empaquetar el tráfico original.

### 🗺️ Topología y Direccionamiento
* **Red Túnel GRE:** 10.10.10.0/30

![Topología VPN 4]
<img width="397" height="275" alt="image" src="https://github.com/user-attachments/assets/2162fcb6-eb40-4f15-b64d-069085f6423a" />


---

## 5. GRE sobre IPSec (con IKEv2)

### 🎯 Objetivo
Encapsular tráfico de red multiprotocolo utilizando túneles GRE (Generic Routing Encapsulation) y proveer confidencialidad e integridad a dicha comunicación aplicando una capa de encriptación IPSec por encima del túnel. (Implementación asegurada con políticas avanzadas de IKEv2).

### ⚙️ Parámetros Criptográficos
* Interfaz operando en `tunnel mode gre ip`. En la Fase 2 de IPSec, el Transform-set se configuró en `mode transport` en lugar de modo túnel; esto es una mejor práctica para evitar una doble encapsulación innecesaria, ya que GRE se encarga de empaquetar el tráfico original. Integración del perfil de IKEv2 para autenticación.

### 🗺️ Topología y Direccionamiento
* **Red Túnel GRE:** 10.10.10.0/30

![Topología VPN 5]
<img width="324" height="261" alt="image" src="https://github.com/user-attachments/assets/d284e9ce-a3f2-4fb5-910a-24f2b7ae9746" />


---

## 6. DMVPN Fase 2 (con IKEv1 y EIGRP)

### 🎯 Objetivo
Desplegar una topología Hub-and-Spoke dinámica y escalable. En la Fase 2, se permite que las sucursales (Spokes) resuelvan sus direcciones mutuamente para establecer túneles VPN directos entre sí bajo demanda, reduciendo la saturación de procesamiento y ancho de banda en el enrutador central (Hub).

### ⚙️ Parámetros Usados
* Protocolo NHRP (Next Hop Resolution Protocol) para el mapeo dinámico de IPs públicas y privadas, interfaces mGRE (Multipoint GRE). Enrutamiento dinámico EIGRP, deshabilitando las funciones `split-horizon` y `next-hop-self` en el Hub para permitir el tráfico Spoke-to-Spoke.

### 🗺️ Topología y Direccionamiento
* **Red DMVPN (Túnel Multipunto):** 172.16.0.0/24
* **Protocolo de Enrutamiento:** EIGRP (AS 100)

![Topología VPN 6]
<img width="361" height="423" alt="image" src="https://github.com/user-attachments/assets/3d1eafc5-2488-420a-a18d-97c66dbb7280" />


---

## 7. DMVPN Fase 3 (con IKEv2 y EIGRP)

### 🎯 Objetivo
Optimizar al máximo el enrutamiento de la red multipunto combinando la eficiencia de la Fase 3 con la seguridad robusta y negociación rápida de IKEv2. Se consolida la tabla de enrutamiento apuntando al Hub, pero se permite la creación de atajos directos entre sucursales mediante redireccionamientos dinámicos.

### ⚙️ Parámetros Usados
* Comando `ip nhrp redirect` en el enrutador central (Hub) para avisar de mejores rutas, y comando `ip nhrp shortcut` en las sucursales (Spokes) para crear los túneles directos. Perfiles de IKEv2 configurados para aceptar conexiones de peers dinámicos (`address 0.0.0.0 0.0.0.0`).

### 🗺️ Topología y Direccionamiento
* **Red DMVPN (Túnel Multipunto):** 172.16.0.0/24
* **Protocolo de Enrutamiento:** EIGRP (AS 100)

![Topología VPN 7] 
<img width="360" height="427" alt="image" src="https://github.com/user-attachments/assets/b9407b45-6824-4f06-9647-c323466136ae" />


---
