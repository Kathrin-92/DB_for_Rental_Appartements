# Project: Creating a Database for Renting Appartements and Bedrooms

## Table of Contents
1. [General Info](#General-Info)
2. [Installation](#Installation)
3. [Usage and Main Functionalities](#Usage-and-Main-Functionalities)
4. [Metadata](#Metadata)
5. [Contributing](#Contributing)


## General Info
This database was developed as part of a university project (B.Sc. Data Science). The task was to create a database for temporary renting of apartments and bedrooms based on the Airbnb use case using SQL. The database features 20 entities including their associated attributes as well as about 20 data-test-entries for each table. The SQL code for the creation of the db and tables, the INSERT INTO statements for the test data, as well as several queries for testing the database can be found here. 

Before the implementation of the SQL code, the first step in my database design was to determine the purpose of the database. As a crucial component of an online platform that implements the booking and renting of accommodations, similar to a reservation system, the following requirements could be defined in phase 1: The database should allow to store information about guests and hosts, as well as about the available accommodations. This also included information about bookings and related invoices / payments. In addition, ratings were to be submitted and stored. 

As part of the entity relationship model, the information was then divided into tables, the attributes were arranged in columns, and the relationships among the tables were defined. The corresponding entity relationship model can be found here as well. During the development of the SQL database, the ERM was further refined and the final project is slightly changed from the initial plan. 


## Installation

**Requirements:** 
This db runs with the open-source version of MySQL. Please install MySQL as well as the MySQL Workbench on your computer to run the code. You can download the latest version of MySQL [here](https://dev.mysql.com/downloads/). MySQL Workbench is available for Windows, Linux, and macOS.


**How To:**
After you've successfully installed your SQL workspace, open your chosen MySQL connection or create a new one for the database. Download the files "create_database.sql", "inserting_values.sql", and  "select_queries.sql" and save them in a folder on your computer. Now you can open the files through your MySQL Workbench and can check out its functionalities. First run the file "create_database.sql" to initialize the db. You are free to download the "inserting_values.sql" for the test data to try out some of the functionalities. With the file "select_queries.sql" there are already some test queries for you to try out.   


## Usage and Main Functionalities

*Please also refer to the comments within the SQL code itself to get more information on the functionalities of the db.*

#### 0. Create Database
* Creates the database as "airbnb_rental"
* Additionally setting the character_set_client, character_set_connection, and character_set_results to utfmb4

---
#### 1. Member
* Table to hold member related data: member_id (pk), first_name, last_name, gender, birthday, profile_picture
* Checks whether the birthday is greater than 01.01.1910 to ensure a valid entry

---
#### 2. login_info
* Table to hold the login information: email, password, member_id (foreign key) 
* Be aware: In this university project, the password is not stored via hash but as plain text
* Constraint ON DELETE & ON UPDATE on the fk

---
#### 3. contact_info
* Table to hold further contact info (optional): phone number, member_id (foreign key) 
* Constraint ON DELETE & ON UPDATE on the fk

---
#### 4. city
* Stores city information: zip_code (pk), city name, country
* Works with an ENUM list for the countries 

---
#### 5. member_address
* Storing address data of the members: address_id (pk), street, house_number, member_id (fk), zip_code (fk) 
* Constraints ON DELETE & ON UPDATE on member_id and zip_code

---
#### 6. coupons
* Optional for members to have coupons/vouchers; corresponding information is stored in this table: coupons_id (pk), description, amount, expiry_date, redeemed, member_id (fk)
* Constraint ON DELETE & ON UPDATE on the fk

---
#### 7. social_media
* Stores if a member has connected their social media accounts (optional): member_id (pk, fk), instagram_url, facebook_url

---
#### 8. payment_methods
* Tables that stores a member's payment method: payment_method_id (pk), paypal_email, paypal_verification, credit_card_no; credit_card_verification, member_id (fk)
* Be aware: application would need to make sure that a member connects at least one payment method
* Constraint ON DELETE & ON UPDATE on the fk

---
#### 9. host
* Creates a table for all hosts on the platform. Every host is automatically a member, but not every member is a host: host_id (pk), member_id (fk) 

---
#### 10. accommodation
* Stores information on all accommodations on the platform: accommodation-id (pk), host_id (fk), description, number_of_guests_possible, price_per_night, availability

---
#### 11. bookings
* Creates booking table containing reservation information: booking_id (pk), start_date, end_date, price_total, accommodation_id (fk), member_id (fk)
* Checks whether end date is after start date
* Be aware: slight variation of this table from the ERM (price_per_night, invoice_id is not saved here) 
* Constraint ON DELETE & ON UPDATE on foreign keys 

---
#### 12. invoice
* Creates invoice table referencing 'bookings' and 'coupons' through foreign keys: invoice_id (pk), booking_id (fk), coupons_id (fk), payable_amount, payment_date, invoice_payed
* Be aware: The 'payable amount' is composed of the price_total minus the amount of a redeemed coupon; is calculated manually in the test data for this project; would need to be calculated automatically by the application 

---
#### 13. payments_to_host
* This table shows the earnings of hosts that provide accommodation on the platform. For this project we assume that the company retains a commission of 7.5%, which is already included in the values here (invoice: payable_amount minus 7.5%): invoice_id (part of the compound pk; fk), host_id (part of the compound pk; fk), payable_amount_tho_host

---
#### 14. cancellations
* The table records whether there was a cancellation of the booking and which invoice is affected: booking_id (part of compound pk; fk), invoice_id (part of compound pk; fk), refund 
* Be aware: In real world settings, the amount of the refund would be based on when the cancellation occurred. For simplicity, it is assumed for this project that the full amount is refunded at any time.

---
#### 15. accommodation_address
* Addresses of available accommodations are stored in this table: acc_address_id (pk), street, house_number, accommodation_id (fk), zip_code (fk) 
* Constraint ON DELETE & ON UPDATE on foreign keys 

---
#### 16. pictures
* Table to store pictures corresponding to an accommodation: picture_id (pk), accommodation_id (fk), file_name, storage_location, alt_text
* Be aware: My image table for this project stores the server paths to each image, not the image itself (via BLOB file format).

---
#### 17. equipment_features
* The table stores the associated apartment properties: accommodation_id (pk, fk), home_type, room_type, has_tv, has_kitchen, has_heating, has_internet, has_aircon, total_bedrooms, total_bathrooms
* Constraint ON DELETE & ON UPDATE on fk 

---
#### 18. ratings
* Creates a tablet hat stores the rating of an accommodation: rating_id (pk), rating, comment, rating_given_by_member_id (fk), rating_for_accommodation_id (fk)
* Constraint ON DELETE & ON UPDATE on foreign keys 
* Be aware: In contrast to the ERM, no INT format was used in the implementation for the rating, but an ENUM list, since the ratings should always have only the values 1 to 5. In addition, the planned attribute "belonging_to_host_id" was deleted, since the associated host can be determined via the accommodation_id.

---
#### 19. instructor
* Simple table containing the data about the instructors of activities: instructor_id (pk), first_name, last_name, email 

---
#### 20. experience
* This table stores information about bookable activities in different cities: activity_id (pk), title, description, availability, group_size, instructor_id (fk), zip_code (fk)
* Constraint ON DELETE & ON UPDATE on foreign keys 
          

## Metadata
* Number of tables: 20
* Number of corresponding entries (test data): 404
* Number of test queries: 29   
* Database size: 640 KiB
* Rows inserted by table incl. Insert Time: 

Table | # rows inserted | insert time

bookings | 25 | 1356.35
contact_info | 20 | 1459.29
Invoice | 25 | 1452.79
member_address | 23 | 1468.13
city | 25 | 796.28
member | 20 | 765.69
Accommodation | 21 | 770.24
login_info | 20 | 953.94
coupons | 20 | 752.44
payment_methods | 20 | 724.77
social_media | 20 | 576.32
payments_to_host | 25 | 491.39
host | 10 | 443.76
accommodation_address | 21 | 201.71
cancellations | 8 | 191.71
experiences | 20 | 154.99
ratings | 20 | 154.61
pictures | 20 | 144.70
equipment_features | 20 | 107.40
instructor | 20 | 87.64


## Contributing 
This is my first SQL project. Your comments, suggestions, and contributions are welcome. 
Please feel free to contribute pull requests or create issues for bugs and feature requests.
