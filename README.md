# MediaWiki - Remote backup

## Descrição
Solução escrita em script shell para backup em site remoto da MediaWiki.

A execução em executada em dois momentos:

1o. Criação de tarball com arquivos da aplicacao e dump.DB;

2o. Sincronização do tarball em maquina remota;

Importante:

a. Para transferencias de arquivos sobre ssh é necessario garantir relacao de confianca baseada em troca de chaves RSA/DSA;

b. Para monitoramento da rotina atraves de email é necessario a configiração de algum smtp client (ex posftix);


```
            +-----------------+  +                                            + +-------------------+            +-------------------+
            |                 |  |                                            | |                   |            |                   |
            |  MEDIAWIKI-DST  |  |  +-----------> XXXXXXXXXXXXXX +----------> | |   MEDIAWIKI-SRC   | +--------> |   MEDIAWIKI-SRC   |
            |   10.81.1.221   |  |    ssh/22      X  internet  X   ssh/22     | |  192.168.105.172  | mysql/3306 |  192.168.105.173  |
            |                 |  |  <-----------+ XXXXXXXXXXXXXX <----------+ | |        app        | <--------+ |         bd        |
            +-----------------+  |                                            | +-------------------+            +-------------------+
            root@10.81.1.221:22  |              # --- Inicio --- #            | userssh@192.168.105.172:22
      ~/backupMediaWIki.sync.sh  |                                            | ~/backupMediaWIki.dump.sh

                                 |                                            |
1. Solicita geracao de tarball:  |  +-------------------------------------->  | 2. Gera tarball unico:
   a. Via ssh invoca geracao;    |                                            |    a. Cria gz da aplicao (DocRoot)
                                 |                                            |    b. Realiza dumpdb em gz
3. Remaneja tarball remoto:      |                                            |    c. Empacota em tarball
   a. Recebe confirmacao;        |  <--------------------------------------+  |    d. Confirma geracao de taball
   b. Realiza sync (scp);        |                                            |    e. Envia email
   c. Calcula e compara hashMD4; |                                            |
   d. Envia email;               +                # --- Fim --- #             +

```
## Agendamento/Job

- Periodicidade : Diária;
- Hora          : 06H00;
- Job           : /home/userssh/JobBackupMediaWikiSRC.sh
- Local         : root@10.81.1.221

## Origem do backup

- End IP Aplicacao: 192.168.105.172
- End IP Banco    : 192.168.105.173
- Dir Aplicação   : /usr/local/mediawiki-1.23.5
- DBName          : WIKI
- DBUser          : mediawiki

## Destino

- End IP Destino    : 10.81.1.221 (infraestrutura remota)
- Dir Destino Backup: /var/backups/MediaWikiSRC
