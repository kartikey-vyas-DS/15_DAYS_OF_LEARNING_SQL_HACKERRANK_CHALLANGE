# 15_DAYS_OF_LEARNING_SQL_HACKERRANK_CHALLANGE
 Julia conducted a  days of learning SQL contest. The start date of the contest was March 01, 2016 and the end date was March 15, 2016.  Write a query to print total number of unique hackers who made at least  submission each day (starting on the first day of the contest), and find the hacker_id and name of the hacker who made maximum number of submissions each day. If more than one such hacker has a maximum number of submissions, print the lowest hacker_id. The query should print this information for each day of the contest, sorted by the date.
# CODE THAT WORKS WITH MYSQL VERSIONS<8
 
select d.submission_date, 
         (
         select count(*) from 
         (select s.submission_date, s.hacker_id,
          @n:=if(count(1) > 1, 1, count(1)) alt,
             (select count(hacker_id)
                  from 
                      (select s.submission_date, s.hacker_id, count(1),
                      @n:=if(count(1) > 1, 1, count(1)) alt
                      from submissions s group by 1, 2 order by 1, 3 desc, 2) t
                where submission_date<=s.submission_date and hacker_id=s.hacker_id) agg
          from submissions s group by 1, 2 order by 1, 3 desc, 2) oi
        where submission_date<=d.submission_date and agg=extract(day from d.submission_date)
         ) uniq_count,
(select s.hacker_id from submissions s inner join hackers h on s.hacker_id = h.hacker_id where s.submission_date = d.submission_date 
        group by s.hacker_id, h.name order by count(s.hacker_id) desc, s.hacker_id asc limit 1) id_highest_sub,
    (select h.name from submissions s inner join hackers h on s.hacker_id = h.hacker_id where s.submission_date = d.submission_date 
        group by s.hacker_id, h.name order by count(s.hacker_id) desc, s.hacker_id asc limit 1) name_highest_sub
from (select distinct s.submission_date from submissions s order by s.submission_date) d
