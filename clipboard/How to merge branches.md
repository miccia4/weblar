Come "fondere" i branch
===================

Appunti su come utilizzare i branch.

Creare un nuovo branch
----------------------

Dal branch `master` creare un nuovo branch con il comando 

  * `git branch nome-branch`,

lavorare sul nuovo ramo creato dopo essersi spostati su di esso:

   * `git checkout nome-branch`.
    
Verosimilmente, quando si dovranno unire i due rami, ci sarà bisogno di fondere le modifiche apportate al master con il nuovo codice contenuto nella branch.
Se invece vogliamo aggiornare il ramo creato con le modifiche effettuate nel master, basta che quel ramo venga dato il comando:

  * `git pull origin master` : se gli aggiornamenti del master non sono ancora stati scaricati
  * `git merge master` : altrimenti

Merge
-----

Per effettuare la fusione, scarichiamo il ramo con un pull e dal ramo master diamo il comando

  * `git merge nome-branch`.

Nel caso in cui non ci siano conflitti i due rami vengono fusi senza problemi. In caso contrario bisogna risolverli a mano editandoli.

Conflitti
---------

Ci sono due "tipi" di conflitti o meglio due sensi. In uno, un file viene modificato in entrambi i branch e si vuole mergiare nel master; nell'altro si vuole aggiornare il branch pullando il codice dal master. Non c'è conflitto se c'è una modifica solo in un branch o se le modifiche vengono fatte solo in cima al file in un branch e solo in fondo al file nell'altro.

Un esempio. Tenendo in considerazione un file `prova.txt` creato nel branch *master* e di conseguenza in un branch chiamato *develop* appena creato:

### 1) Merge dal master

 Alla creazione del branch *develop* il file conteneva la frase
 
    Luca@Tiger ~/git/test-repo (develop)
    $ cat prova.txt
    Provare non costa niente.

  modificato il file
  
    Luca@Tiger ~/git/test-repo (develop)
    $ cat prova.txt
    Provare non costa proprio niente.
  
  Mentre nel *master* (occhio se fate questa prova sulla stessa shell e cambiate ramo con `git chechout master` avrete un merge automatico)
  
    Luca@Tiger ~/git/test-repo (master)
    $ cat prova.txt
    Provare non costa niente.  
 
 cambiato con
 
    Luca@Tiger ~/git/test-repo (master)
    $ cat prova.txt
    Ecco. Provare non costa niente.  

  Ora nel *master* vogliamo mergiare con il ramo *develop*
  
    Luca@Tiger ~/git/test-repo (master)
    $ git merge develop
    Auto-merging prova.txt
    CONFLICT (content): Merge conflict in prova.txt
    Automatic merge failed; fix conflicts and then commit the result.
    
    Luca@Tiger ~/git/test-repo (master|MERGING)
    $

  notare come ci viene mostrato che c'è un conflitto, che è fallito un merge automatico e quindi siamo in una situazione **transiente** denotata dalla dicitura `(master|MERGING)` e siamo chiamati ad uscirne.
  
  Con il comando `git diff` possiamo vedere quali sono le parti di codice che hanno generato il conflitto. Aprendo il file prova
  
    Luca@Tiger ~/git/test-repo (master|MERGING)
    $ cat prova.txt
    <<<<<<< HEAD
    Ecco. Provare non costa niente.
    =======
    Provare non costa proprio niente.
    >>>>>>> develop

  avremo chiara la situazione. Per sbrogliare la matassa editiamo il file cancellando i tag e lasciando ciò che ci interessa e/o aggiungendo dell'altro. Per esempio
    
    Luca@Tiger ~/git/test-repo (master|MERGING)
    $ cat prova.txt
    Ecco. Provare non costa proprio niente. Visto?

  Ora con `git diff` possiamo vedere cosa c'era nelle due versioni dei file e cosa c'è ora.
  
    Luca@Tiger ~/git/test-repo  (master|MERGING)
    $ git diff
    diff --cc prova.txt
    index 71171d8,a3b81e0..0000000
    --- a/prova.txt
    +++ b/prova.txt
    @@@ -1,1 -1,1 +1,1 @@@
    - Ecco. Provare non costa niente.
     -Provare non costa proprio niente.
    ++Ecco. Provare non costa proprio niente. Visto?

  Il conflitto ancora non è risolto perchè bisogna comunicare a git che è tutto ok, infatti se diamo il comando `git status` ci dirà che c'è un file che è stato modificato da entrambe le parti
  
    $ git status
    # On branch master
    # Unmerged paths:
    #   (use "git add/rm <file>..." as appropriate to mark resolution)
    #
    #       both modified:      prova.txt
    #
    no changes added to commit (use "git add" and/or "git commit -a")

    $ git status -s
    UU prova.txt

  e per fare ciò basta dare il comando `git add prova.txt` e committare. Il merge è finito andate in pace.
  
###2) PULL DAL BRANCH

  Stessa situazione iniziale, ma l'aggiornamente viene fatto nel ramo *develop* tramite il comando `git pull origin master` o `git merge`. Stessa procedura per risolvere il conflitto.
    
Importante, o in ogni caso indubbiamente comodo, è avere tra parentesi il nome del branch in cui si è posizionati. In alternativa consiglio di creare un alias per non impazzire nello scrivere ripetutamente `git branch`; per esempio `alias gb='git branch -v'`.

_______________________

## Comandi utili per le branch

 * `git log --oneline --graph` : visualizza graficamente sulla bash una timeline con push e branch
 * `git branch -b nome-branch` : crea una nuova branch e ti posiziona subito dentro di essa

## Riferimenti

> http://gitref.org/branching/#merge
