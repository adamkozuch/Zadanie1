#1a. Import csv do mongo i postgress

##*import do bazy mongo
time mongoimport --db test --collection trainCollection  --type csv --headerline --file /home/adam/Downloads/Train_.csv 

 
Import jest zakonczony z nastepujacymi wynikami:
![](https://cloud.githubusercontent.com/assets/5136443/5113019/7df80296-702c-11e4-967f-9fa70151854d.png)

Zostalo zaimportowanych 6034195 obiektow a import trwal 23 minuty 47 sekund.

 



 

##*import do bazy postgress 
-tworzymy tabele za pomocą polecenia 
#####create table traintable1( Id text, Title text, Body text, Tags text);



-Używamy polecenia copy aby załadować rekordy

#####COPY trainTable1(Id,Title,Body, Tags) FROM '~Downloads/Train_.csv' WITH DELIMITER ',' CSV HEADER
Niestety nie udalo mi sie uzyc polecenia explain analyze na tym zapytaniu ale import trwal 32 minuty.


#1b Zliczenie liczby rekordów 
W mongo uzywamy 
#####db.train.count() 
wynik jest następujący
![](https://cloud.githubusercontent.com/assets/5136443/5113346/358b55f0-702f-11e4-8908-8fde58ce2f26.png)

 
Jak widać w bazie znajduje 6034195 rekordow.
Zuzycie pamieci i procesora podczas tego procesu jest nastepujace 
![](https://cloud.githubusercontent.com/assets/5136443/5113017/7df7102a-702c-11e4-9907-e6006824a261.png)




W postgressie używamy
#####Select count(*) from tabletrain 
rezultat jest następujący 
Tutaj zdjecie ze zliczenie









#Zadanie 1c

#*wykonanie w postgress
Tabela zawierająca wczytane dane nazywa się tabletrain. Aby zmienić string na tablicę stringów uzywamy w postgressien polecenia 

#####ALTER TABLE tabletrain ALTER COLUMN TAGS TYPE TEXT[] using string_to_array(tags,' ') ;

Nastepnie zliczamy ilośc rekordów poleceniem 

#####Select sum(array_length(tags,1)) from tabletrain;
![](https://cloud.githubusercontent.com/assets/5136443/5113011/7dde53fa-702c-11e4-83d0-1c066f4980e1.png)
W bzaie znajduje sie 17409994 tagow.

Zeby liiczyc ilosc roznych tagow uzywamy zapytania 
#####select  count(*) from (select distinct unnest(tags) from tabletrain) as foo. Wynik jest nastepujacy

![](https://cloud.githubusercontent.com/assets/5136443/5113015/7deae372-702c-11e4-9166-79ecdac2f85b.png)
W bazie znajduje sie 42048 roznych tagow.

##Druga czesc zadania 
Natomiast w drugiej części zadania został uzyty sterowniki Javy


*Sterownik Java 
Zadanie jest wykonane za pomocą następującego kodu:


iimport java.net.UnknownHostException;
import java.util.HashSet;
import java.util.Set;

import com.mongodb.BasicDBObject;
import com.mongodb.DB;
import com.mongodb.DBCollection;
import com.mongodb.DBCursor;
import com.mongodb.MongoClient;
 
public class JavaAgreggations {
 
    public static void main(String[] args) {
        try {
             
            MongoClient mongoClient = new MongoClient("localhost");
             
           DB database = mongoClient.getDB("test");
             
        DBCollection collection = database.getCollection("trainCollection");
      
        BasicDBObject query = new BasicDBObject(); 
        
        BasicDBObject fields=new BasicDBObject(); 
        
        fields.put("Tags", 1);
         
          DBCursor cursor = collection.find(query,fields );
          
          int numberOfTags =0;
          
          Set<String> tagsWithoutDuplicates = new HashSet<String>();
          
          long start = System.currentTimeMillis(); 
          
          
        	while(cursor.hasNext()) {
        		
        String tags =cursor.next().get("Tags").toString();
       String[] tableOftags= tags.split(" ");
       numberOfTags+=tableOftags.length;
       
       for(int in=0;in<tableOftags.length;in++)
       {
    	   tagsWithoutDuplicates.add(tableOftags[in]);
       }
       
       }
        
        	long elapsedTime = System.currentTimeMillis() - start;
        	System.out.println(numberOfTags);
        	System.out.println("Ilośc rożnych tagów "+tagsWithoutDuplicates.size());
        	
        	System.out.println("Czas potrzebny na zrealizowanie zadania :"+elapsedTime );
  	           
            mongoClient.close();
             
        } catch (UnknownHostException ex) {
            ex.printStackTrace();
        }
         
    }
}

Wyniki wykonania kodu sa nastepujace:
![](https://cloud.githubusercontent.com/assets/5136443/5113013/7de34cac-702c-11e4-8140-3e583040edc6.png)
Zadanie bylo realizowane przez 178 sekund.Natomiast obciazenie komputera bylo nastepujace 
![](https://cloud.githubusercontent.com/assets/5136443/5113023/7e0c3b3a-702c-11e4-8c42-bde61ea6f91d.png)
 