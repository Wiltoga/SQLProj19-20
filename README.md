<!---
Un aperçu du markdown est disponible à cette adresse :
https://github.com/WildGoat07/SQLProj19-20/blob/master/README.md
-->
# Projet SQL 2019 - 2020

Chaque requête est écrite de manière compatible (par rapport au cours) et d'une manière plus conventionnelle (optimisation, lisibilité) mais utilise des élements non vus en cours.

## requêtes :

1. conventionnelle :
    ```sql
    -- simple jonction entre deux tables
    select lib_reponse
    from rep_proposee
    inner join question on rep_proposee.no_question = question.no_question
    where etat_rep = true
    and question.no_question = 3;
    ```
    compatible :
    ```sql
    -- simple jonction entre deux tables
    select lib_reponse
    from rep_proposee, question
    where etat_rep = true
    and question.no_question = 3
    and rep_proposee.no_question = question.no_question;
    ```
1. conventionnelle :
    ```sql
    -- jonctions multiples et des conditions
    select lib_reponse
    from rep_proposee
    inner join question on question.no_question = rep_proposee.no_question
    inner join rep_donnee on rep_donnee.no_question = question.no_question
    and rep_proposee.no_ordre = rep_donnee.no_ordre
    inner join quest_session on rep_donnee.no_session = quest_session.no_session
    inner join personne on quest_session.no_pers = personne.no_pers
    where quest_session.no_session = 12
    and nom_pers = lower("HOCHET")
    and prenom_pers = lower("Ric")
    and question.no_question = 4;
    ```
    compatible :
    ```sql
    -- jonctions multiples et des conditions
    select lib_reponse
    from rep_proposee, question, rep_donnee, quest_session, personne
    where quest_session.no_session = 12
    and nom_pers = lower("HOCHET")
    and prenom_pers = lower("Ric")
    and question.no_question = 4
    and question.no_question = rep_proposee.no_question
    and rep_donnee.no_question = question.no_question
    and rep_proposee.no_ordre = rep_donnee.no_ordre
    and rep_donnee.no_session = quest_session.no_session
    and quest_session.no_pers = personne.no_pers;
    ```
1. conventionnelle :
    ```sql
    -- on crée une table contenant une seule case (colonne 'c') donnant le nombre d’utilisateurs différents ayant lancé minimum 2 fois un même questionnaire
    create table quest_started as
    select count(distinct no_pers) as c
    from quest_session
    group by no_pers, no_quest
    having count(no_quest) >= 2;
    -- on crée une table qui contient une seule case (colonne 'c') qui indique le nombre total d'utilisateurs
    create table user_count as
    select count(*) as c
    from personne;
    -- on renvoie le pourcentage (et on ne multiplie PAS un pourcentage par 100, c’est au programme/site appelant de le faire pour le formattage !!!)
    select quest_started.c/user_count.c pourcentage
    from quest_started, user_count;
    -- on se débarrasse des tables temporaires
    drop table quest_started;
    drop table user_count;
    ```
    compatible :
    ```sql
    -- on crée une table qui contient une seule case (colonne 'c') contenant le nombre d'utilisateurs ayant démarré un même questionnaire plusieurs fois
    create table quest_started as
    select count(distinct qs1.no_pers) c
    from quest_session qs1, quest_session qs2
    where qs1.no_pers = qs2.no_pers
    and qs1.no_quest <> qs2.no_quest;
    -- on crée une table qui contient une seule case (colonne 'c') qui indique le nombre total d'utilisateurs
    create table user_count as
    select count(*) c
    from personne;
    -- on affiche le pourcentage
    select quest_started.c/user_count.c percent
    from quest_started, user_count;
    -- on se débarrasse des tables temporaires
    drop table quest_started;
    drop table user_count;
    ```
