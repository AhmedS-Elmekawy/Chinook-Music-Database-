# Chinook-Music-Database-
Data Analysis Using SQLite 

In this project, we will query the Chinook Database. The Chinook Database holds information about a music store. For this project, we will be assisting the Chinook team with understanding the media in their store, their customers and employees, and their invoice information. To assist you in the queries ahead

Question 1: Which countries have the most Invoices?
Use the Invoice table to determine the countries that have the most invoices. Provide a table of BillingCountry and Invoices ordered by the number of invoices for each country. The country with the most invoices should appear first.

SELECT BillingCountry, Count(*) AS Invoices
FROM Invoice
GROUP BY BillingCountry
ORDER BY Invoices DESC
LIMIT 10;

Question 2: Which city has the best customers?
We want to throw a promotional Music Festival in the city we made the most money. Write a query that returns the 1 city that has the highest sum of invoice totals. Return both the city name and the sum of all invoice totals.

SELECT BillingCity, SUM(total) as Total_Invoices
FROM Invoice
GROUP BY BillingCity
ORDER BY Total_Invoices DESC;

Question 3: Who is the best customer?
The customer who has spent the most money will be declared the best customer. Build a query that returns the person who has spent the most money. I found the solution by linking the following three: Invoice, InvoiceLine, and Customer tables to retrieve this information, but you can probably do it with fewer!

SELECT c.CustomerId, c.FirstName, c.LastName, sum(total) Total_invoices
FROM Customer c
JOIN Invoice i
ON i.CustomerId = c.CustomerId
GROUP BY 1,2,3
ORDER BY 4 DESC;

Question 4

Use your query to return the email, first name, last name, and Genre of all Rock Music listeners (Rock & Roll would be considered a different category for this exercise). Return your list ordered alphabetically by email address starting with A.

SELECT c.Email, c.FirstName, c.LastName, g.Name Genre 
FROM Customer c
JOIN Invoice i
ON c.CustomerId = i.CustomerId
JOIN InvoiceLine il
ON i.InvoiceId = il.InvoiceId
JOIN Track t
ON t.TrackId = il.TrackId
JOIN Genre g
ON g.GenreId = t.GenreId
WHERE g.GenreId = 1
GROUP BY 1,2,3,4
ORDER BY 1;

Question 5: Who is writing the rock music?

Now that we know that our customers love rock music, we can decide which musicians to invite to play at the concert.

SELECT a.ArtistId, a.Name, COUNT(*) songs
FROM Artist a
JOIN Album al
ON a.ArtistId = al.ArtistId
JOIN Track t
ON al.AlbumId = t.AlbumId
JOIN Genre g
ON t.GenreId = g.GenreId
WHERE g.Name = 'Rock'
GROUP BY 1,2
ORDER BY 3 DESC
LIMIT 10;

Question 6

First, find which artist has earned the most according to the InvoiceLines?

SELECT ar.Name, SUM(il.UnitPrice) AS amount_spent
FROM InvoiceLine il
JOIN Track t
ON il.TrackId = t.TrackId
JOIN Album al
ON t.AlbumId = AL.AlbumId
JOIN Artist ar
ON al.ArtistId = ar.ArtistId
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;

Now use this artist to find which customer spent the most on this artist.

SELECT c.CustomerId, c.FirstName, c.LastName, ar.Name, sum(il.Quantity*il.UnitPrice) AmountSpent
FROM Invoice i
JOIN InvoiceLine il
ON i.InvoiceId = il.InvoiceId
JOIN Track t
ON il.TrackId = t.TrackId
JOIN Customer c
ON i.CustomerId = c.CustomerId
JOIN Album al
ON t.AlbumId = al.AlbumId
JOIN Artist ar
ON al.ArtistId = ar.ArtistId
WHERE ar.Name = 'Iron Maiden'
GROUP BY 1,2,3,4
ORDER by 5 DESC;


Question 7

We want to find out the most popular music Genre for each country. We determine the most popular genre as the genre with the highest amount of purchases. Write a query that returns each country along with the top Genre. For countries where the maximum number of purchases is shared, return all Genres.

SELECT count(*) purchases, c.Country, g.Name, g.GenreId
FROM Customer c
JOIN Invoice i
on c.CustomerId = i.CustomerId
JOIN InvoiceLine il
ON i.InvoiceId = il.InvoiceId
JOIN Track t
ON il.TrackId = t.TrackId
JOIN Genre g
ON t.GenreId = g.GenreId
GROUP BY 2,4
ORDER BY 1,2 ;


Question 8
Return all the track names that have a song length longer than the average song length. Though you could perform this with two queries. Imagine you wanted your query to update based on when new data is put in the database. Therefore, you do not want to hard code the average into your query. You only need the Track table to complete this query.

Return the Name and Milliseconds for each track. Order by the song length with the longest songs listed first.

SELECT t.Name Track_name, t.Milliseconds, g.Name genre_name
FROM Track t
JOIN Genre g
ON t.GenreId = g.GenreId
WHERE t.Milliseconds > (SELECT AVG(Milliseconds) FROM Track)
ORDER BY 2 DESC;

Question 9
Write a query that determines the customer that has spent the most on music for each country. Write a query that returns the country along with the top customer and how much they spent. For countries where the top amount spent is shared, provide all customers who spent this amount.

WITH total_spent_table AS (

SELECT c.FirstName, c.LastName, c.Country, c.CustomerId, SUM(i.total) AS sum_invoices
FROM Customer c
JOIN Invoice i
ON c.CustomerId = i.CustomerId
GROUP BY i.CustomerId

)

SELECT total_spent_table.* FROM Total_spent_table
LEFT JOIN (

	SELECT CustomerId, FirstName, LastName, Country, MAX(sum_invoices) AS MaxInvoices
	FROM total_spent_table
	GROUP BY Country
	
)Max_spent_table

ON Total_spent_table.Country = Max_spent_table.Country
WHERE total_spent_table.sum_invoices = Max_spent_table.MaxInvoices
ORDER BY Country;
