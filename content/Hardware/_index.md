---
date: '2026-03-24T18:43:16-03:00'
draft: false
title: 'Hardware'
weight: 50
cascade:
    type: docs
---

O Zumbi atualmente é composto por um nó de login (zumbi.padmec.org) e três nós computacionais, n01, n02, e n03.

O nó de login é uma relíquia do nosso primeiro cluster "profissional", instalado em 2012, mais ou menos, e está sendo mantido operacional a custa de bandaids e muito muito suor. Ele é composto por 

- 2 processadores AMD Opterom 6272 de 2.1GHz clock base, com 16 núcleos cada.
- 128 GB de memória RAM
- 8 discos Sata montados em RAID 5 pelo BIOS, em um total de aproximadamente 20TB de storage
- 2 interfaces Infiniband de 40 Gbps
- Várias interfaces Ethernet 1 Gbps 

Os nós computacionais são muito mais modernos, e são compostos, cada um, por 
    
- 2 processadores AMD EPYC 7713 de 3.7 GHz clock base, com 64 núcleos cada
- 256 GB de memória RAM
- 1 disco Sata de 8TB (não usado no momento)
- 1 disco SSD de 1TB usado como área de scratch
- 2 interfaces Infiniband de 40 Gbps
- Várias interfaces Ethernet 1 Gbps 
- Uma GPU Nvidia RTX A2000 com 6GB de memória de video

O cluster tem então 384 núcleos computacionais quando está operando a plena capacidade.

Os switches, tanto Infiniband quando Ethernet, são da mesma época do nó de login.
