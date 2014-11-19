<<<<<<< HEAD
1a Import csv do mongo i postgress

*import do bazy mongo
time mongoimport --db test --collection trainCollection  --type csv --headerline --file /home/adam/Downloads/Train_.csv 


-Zrzuty z monitora systemu

oraz






 

*import do bazy postgress 
-tworzymy tabele za pomocą polecenia 
create table traintable1( Id text, Title text, Body text, Tags text);
-Używamy polecenia copy aby załadować rekordy

COPY trainTable1(Id,Title,Body, Tags) FROM '~Downloads/Train_.csv' WITH DELIMITER ',' CSV HEADER



1b Zliczenie liczby rekordów 
W mongo uzywamy db.train.count() wynik jest następujący


Jak widać w bazie znajduje się oczekiwana liczba rekordów




W postgressie używamy Select count(*) from trainTable1 rezultat jest następujący 


Tutaj to można zrobić jeszcze raz  ze zliczeniem







Zadanie 1c


Aby zmienić string na tablicę stringów uzywamy w postgressien polecenia 

ALTER TABLE tabletrain ALTER COLUMN TAGS TYPE TEXT[] using string_to_array(tags,' ') ;

Nastepnie zliczamy ilośc rekordów poleceniem 

Select sum(array_length(tags,1)) from tabletrain;

1d.Sterownik Java 
Zadanie jest wykonane za pomocą następującego kodu


import java.net.UnknownHostException;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

import org.bson.BSON;

import com.mongodb.BasicDBObject;
import com.mongodb.DB;
import com.mongodb.DBCollection;
import com.mongodb.DBCursor;
import com.mongodb.DBObject;
import com.mongodb.MongoClient;

public class JavaAgreggations {

	public static void main(String[] args) {
		try {

			MongoClient mongoClient = new MongoClient("localhost");

			DB database = mongoClient.getDB("test");

			DBCollection collection = database.getCollection("trainCollection");

			BasicDBObject query = new BasicDBObject();

			BasicDBObject fields = new BasicDBObject();

			fields.put("Tags", 1);

			DBCursor cursor = collection.find(query, fields);

			int numberOfTags = 0;

			Set<String> tagsWithoutDuplicates = new HashSet<String>();

			long start = System.nanoTime();

			while (cursor.hasNext()) {

				String tags = cursor.next().get("Tags").toString();
				String[] tableOftags = tags.split(" ");
				numberOfTags += tableOftags.length;

				for (int in = 0; in < tableOftags.length; in++) {
					tagsWithoutDuplicates.add(tableOftags[in]);
				}

			}

			long elapsedTime = System.nanoTime() - start;
			System.out.println(numberOfTags);
			System.out.println("Ilośc rożnych tagów "
					+ tagsWithoutDuplicates.size());

			System.out.println("Czas potrzebny na zrealizowanie zadania :"
					+ elapsedTime);

			mongoClient.close();

		} catch (UnknownHostException ex) {
			ex.printStackTrace();
		}

	}
}

Wynikiem wykonania kodu są następujące wyniki 

Jeśli chodzi o zurzycie pamięci oraz procesora wygląda ono następująco:







=======
![](https://cloud.githubusercontent.com/assets/5136443/5090734/72036842-6f44-11e4-9388-a344b8c63ced.png)
>>>>>>> 1fd4d3ea72a5529f6de0339b155d285641cea681
