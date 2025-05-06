## Mise en place de la vm et du terminal
Nous avons télécharger hadoop, puis nous avons suivis les étapes du tp hadoop pour mettre en place la vm et les commandes a entrer dans le cmd. 

Nous avons également contourner l'installation de "sudo", "nano" etc, car la version de python n'était pas la bonnenous avons donc detourner et installer la bonne version pour le tout afin que sa fonctionne. 

# Question

## Question 1 :  savoir combien de tags possede chaque film

### Script 

from mrjob.job import MRJob
from mrjob.step import MRStep

class MovieTagCount(MRJob):
    def steps(self):
        return [
            MRStep(mapper=self.mapper_get_tags,
                   reducer=self.reducer_count_tags)
        ]

    def mapper_get_tags(self, _, line):
        try:
            userId, movieId, tag, timestamp = line.split(',')
            yield movieId, 1
        except:
            pass

    def reducer_count_tags(self, movieId, counts):
        yield movieId, sum(counts)

if __name__ == '__main__':
    MovieTagCount.run()

### Résultat head 

"1"     697
"10"    137
"100"   18
"1000"  10
"100001"        1
"100003"        3
"100008"        9
"100017"        9
"100032"        2
"100034"        19

Ici on voit la repartition de tags associer à un film, par exemple l'ID 100 a eu 18 tags.

## Question 2 : Savoir combien de tags chaque user a ajoutés

### Script 
from mrjob.job import MRJob
from mrjob.step import MRStep

class UserTagCount(MRJob):
    def steps(self):
        return [
            MRStep(mapper=self.mapper_get_tags,
                   reducer=self.reducer_count_tags)
        ]

    def mapper_get_tags(self, _, line):
        try:
            userId, movieId, tag, timestamp = line.split(',')
            yield userId, 1
        except:
            pass

    def reducer_count_tags(self, userId, counts):
        yield userId, sum(counts)

if __name__ == '__main__':
    UserTagCount.run()


### Résultat head

"100001"        9
"100016"        50
"100028"        4
"100029"        1
"100033"        1
"100046"        133
"100051"        19
"100058"        5
"100065"        2
"100068"        19

On voit que le user 100029 à réalisé 1 tags ce qui n'est pas beaucoup comparer à des users comme le 100046 ou 100001.
## Question 3 : Combien de blocs le fichier occupe-t-il dans HDFS sachant qu'un bloc represente 128Mo


notre fichier fait une taille de 38.8 Mo

### Config. Taille de bloc par défaut
taille de bloc : 128 Mo
ce qui fait qu'on atteins pas la taille d'un bloc donc tout rentre dans le bloc par défaut

### Config. Taille de bloc par personnalisée
taille de bloc : 64 Mo
ce qui fait qu'on atteins 60% de la taille d'un bloc donc tout rentre dans le bloc personnalisée

## Question 4 : Combien de fois chaque tag a été utilisés

### Script 
from mrjob.job import MRJob
from mrjob.step import MRStep

class TagUsageCount(MRJob):
    def steps(self):
        return [
            MRStep(mapper=self.mapper_get_tags,
                   reducer=self.reducer_count_usage)
        ]

    def mapper_get_tags(self, _, line):
        try:
            userId, movieId, tag, timestamp = line.split(',')
            yield tag, 1
        except:
            pass

    def reducer_count_usage(self, tag, counts):
        yield tag, sum(counts)

if __name__ == '__main__':
    TagUsageCount.run()


### Résultat head

" Alexander Skarsg\u00e5rd"     1
" Difficult to find it" 1
" Filmes Antigos "      2
" Filmes Antigos"       2
" Kartik Aaryan"        1
" Kriti Sanon"  1
" Laurel Canyon"        1
" Luis Brandoni"        1
" Masami Nagasawa"      1
" O'Shea Jackson Jr."   1

Globalement chaque tag est unique, mais on remarque quelques doublons.

## Question Bonus : Combien de tags le meme user a introduit pour chaque film

### Script 
from mrjob.job import MRJob
from mrjob.step import MRStep

class UserTagsPerMovie(MRJob):
    def steps(self):
        return [
            MRStep(mapper=self.mapper_get_user_movie_tags,
                   reducer=self.reducer_count_user_movie_tags)
        ]

    def mapper_get_user_movie_tags(self, _, line):
        try:
            userId, movieId, tag, timestamp = line.split(',')
            yield (movieId, userId), 1
        except:
            pass

    def reducer_count_user_movie_tags(self, key, counts):
        yield key, sum(counts)

if __name__ == '__main__':
    UserTagsPerMovie.run()

### Résultat head

["1", "100538"] 4
["1", "10231"]  2
["1", "102568"] 4
["1", "102901"] 1
["1", "103368"] 1
["1", "103371"] 1
["1", "103883"] 3
["1", "104394"] 9
["1", "1048"]   1
["1", "105717"] 1

Le film possédant l'id 1 a reçu 2 tags par le user 10231 et 4 par le user 102568.

# Commande hdfs dans le cmd

### Commande pour script python 

nano test_exemple_md.py, cette commande ouvre une fenetre ou on insere notre script, puis on enregistre avec ctrl o et on sort de la avec ctrl x

### Commande pour executer

python test_exemple_md.py -r hadoop --hadoop-streaming-jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar hdfs:///user/maria_dev/tags.csv -o hdfs:///user/maria_dev/output_test_exemple_md

en executant cette commande le script s'execute et on voit si il y a des erreur ou non, une fois bien executer aucune erreur ne s'affiche.

### Commande pour afficher le head du fichier.py

hdfs dfs -cat /user/maria_dev/output_test_exemple_md/part-* | head

### Commande pour avoir la taille d'un fichier

hdfs dfs -du -h /user/maria_dev/tags.csv

# Lien du GitHub

### Question 1 :
https://raw.githubusercontent.com/Azadkara1/exam_big_data_KARA_TITAOU/refs/heads/main/Question1_resultat

### Question 2 : 

https://raw.githubusercontent.com/Azadkara1/exam_big_data_KARA_TITAOU/refs/heads/main/Question2_resultat


### Question 4 : 

https://raw.githubusercontent.com/Azadkara1/exam_big_data_KARA_TITAOU/refs/heads/main/Question4_resultat

### Question bonus : 

https://raw.githubusercontent.com/Azadkara1/exam_big_data_KARA_TITAOU/refs/heads/main/Questionbonus_resultat