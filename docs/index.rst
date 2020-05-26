NGINX Plus ADC Lab - Index
============================================

Welcome
-------

.. Welcome to the |classbold| lab at F5 / NGINX Training |year|

.. |repoinfo|

The goal of this lab is to familiarize BIG-IP users/engineers with core NGINX+ capabilities and operations, including where to use BIG-IP vs NGINX+ and how they complement each other. The lab will provide basic hands-on configuration concepts.
Subsequent use cases covered in this training use NGINX Plus as their foundational building block.
This lab will survey NGINX Plus feature which differentiate the offering from NGINX open source.

The lab environment provided will give you access to the following infrastructure: 

===================        ==================  ==============================================
  System                    Hostname                        Description
===================        ==================  ==============================================
  Windows Jump Host        ip-10-1-1-4          Windows JumpHost
  Docker Host              ip-10-1-1-5          Centos7 running DockerCE providing upstreams
  NGINX Plus Master        ip-10-1-1-6          NGINX Plus Instance (where you will perform configuration)
  NGINX Plus 2             ip-10-1-1-7          NGINX Plus Instance (for cluster demo)
  NGINX Plus 3             ip-10-1-1-8          NGINX Plus Instance (for cluster demo)
  BIG-IP1                  ip-10-1-1-9          F5 BIG-IP
===================        ==================  ==============================================

Here are the credentials needed for this lab.

=================    ====================  =====================   ===================
  System                  Hostname              User                    Password
=================    ====================  =====================   ===================
Windows Jump Host       ip-10-1-1-4             user                      user         
BIG-IP1                 ip-10-1-1-9             admin                     admin
=================    ====================  =====================   ===================


.. toctree::
   :maxdepth: 2
   :caption: Contents:
   :glob:

   class*/class*
..   examples
..   markdown
