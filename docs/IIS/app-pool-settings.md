---
title: ���ε{�����ϳ]�w
layout: default
parent: IIS
---

#### �ҰʼҦ�
- AlwaysRunning : �û��O���Ұʪ��A�A���|�]���S���ϥΪ̦s���������C
- OnDemand�G�u�����ϥΪ̦s�������ɡA�~�|�ҰʡC
![Start Mode](images/start-mode.png)


�T�w�ɶ����j���S���ϥΪ̦s�������AIIS �|�NApplication Pool�����A�o�ɦA�s�������ɷ|���@�q�ɶ�������C���F�קK�o�Ӱ��D�A�i�H�]�w���ε{������ AlwaysRunning �ݩʬ� True�C

#### �T�w�ɶ����j
�qIIS6�_�AApplication Pool �N���w�����Ҿ���A�H�ѨM�{���]�[�i��X�{�O���鬪�|(Memory Leaking)�����D�C�o���ݩʹw�]�Ȭ� 1740 ��, �]�N�O�C�j 29 �p�ɡAIIS �|�w���Ұʦ^��(Recyling)����C

- �p�G�]�w�� 0, �h��ܥû����Ұʦ^���C
- �i�z�L�u�S�w�ɶ��v���ݩʡA���w���W�ɶ��ӱҰʦ^������C
![Recycle](images/recycle.png)


![App Pool Advence Settings](images/app-pool-advence-settings.png)


![Website Advence Settings](images/website-advence-settings.png)


![Service Unavailable](images/service-unavailable.png)


![Err Connection Refused](images/err-connection-refused.png)