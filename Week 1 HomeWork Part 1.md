#de-zoomcamp

## DE-ZOOMCAMP 2023 - Week 1

### Homework 1 Docker and SQL


#### Question 1: Knowing docker tags

Which tage has the following text? - *Write the image ID to the file*
```
$ docker build --help

Usage:  docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile

Options:
      --add-host list           Add a custom host-to-IP mapping (host:ip)
      --build-arg list          Set build-time variables
      --cache-from strings      Images to consider as cache sources
      --cgroup-parent string    Optional parent cgroup for the container
      --compress                Compress the build context using gzip
      --cpu-period int          Limit the CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int           Limit the CPU CFS (Completely Fair Scheduler) quota
  -c, --cpu-shares int          CPU shares (relative weight)
      --cpuset-cpus string      CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string      MEMs in which to allow execution (0-3, 0,1)
      --disable-content-trust   Skip image verification (default true)
  -f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm                Always remove intermediate containers
      --iidfile string          Write the image ID to the file
      --isolation string        Container isolation technology
      --label list              Set metadata for an image
  -m, --memory bytes            Memory limit
      --memory-swap bytes       Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --network string          Set the networking mode for the RUN instructions during build (default "default")
      --no-cache                Do not use cache when building the image
      --pull                    Always attempt to pull a newer version of the image
  -q, --quiet                   Suppress the build output and print image ID on success
      --rm                      Remove intermediate containers after a successful build (default true)
      --security-opt strings    Security options
      --shm-size bytes          Size of /dev/shm
  -t, --tag list                Name and optionally a tag in the 'name:tag' format
      --target string           Set the target build stage to build.
      --ulimit ulimit           Ulimit options (default [])
```

#### Question 2: Understanding docker first run 
Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash. Now check the python modules that are installed ( use pip list). How many python packages/modules are installed?


```
docker run -it python:3.9 bash

/# pip list
Package    Version
---------- -------
pip        22.0.4
setuptools 58.1.0
wheel      0.38.4
```
#### Question 3. Count records

How many taxi trips were totally made on January 15?
Tip: started and finished on 2019-01-15.

Remember that lpep_pickup_datetime and lpep_dropoff_datetime columns are in the format timestamp (date and hour+min+sec) and not in date.

```
SELECT
	COUNT(1)
FROM 
	green_taxi_data
WHERE 
	CAST(lpep_pickup_datetime AS DATE) = TIMESTAMP '2019-01-15' 
	AND 
	CAST(lpep_dropoff_datetime AS DATE) = TIMESTAMP '2019-01-15';
```
**Output**
```
20530
```

#### Question 4. Largest trip for each day
Which was the day with the largest trip distance Use the pick up time for your calculations.

```
SELECT 
	CAST(lpep_pickup_datetime AS DATE) as "pickup_date",
	MAX(trip_distance) as "longest_trip"
FROM 
	green_taxi_data
GROUP BY 
	CAST(lpep_pickup_datetime AS DATE)
ORDER BY 
	"longest_trip" DESC
Limit 1;
```
**Output**
```
"pickup_date"	"longest_trip"
"2019-01-15"	117.99
```

#### Question 5. The number of passengers
In 2019-01-01 how many trips had 2 and 3 passengers?

```
SELECT 
 	CAST(lpep_pickup_datetime AS DATE) as "pickup",

	passenger_count, 
	COUNT(1)
FROM 
	green_taxi_data
GROUP BY 
	CAST(lpep_pickup_datetime AS DATE),
	passenger_count
HAVING 
	CAST(lpep_pickup_datetime AS DATE) = TIMESTAMP '2019-01-01' 
	AND
	passenger_count IN (2, 3);
```

**Output**
```
"pickup"	 "passenger_count"	"count"
"2019-01-01"	    2	              1282
"2019-01-01"	    3	              254
```

#### Question 6. Largest tip
For the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip? We want the name of the zone, not the id.

```sql

SELECT 
	zpu."Zone" as "pickup_loc", 
	zdo."Zone" as "dropoff_loc", 
	t.tip_amount as "tip"
FROM 
	green_taxi_data t
	Left JOIN 
		zones zpu
	ON 
		t."PULocationID" = zpu."LocationID"
	LEFT JOIN 
		zones zdo
	ON
		t."DOLocationID" = zdo."LocationID"
WHERE 
	zpu."Zone" = 'Astoria'
ORDER BY 
	tip_amount DESC
LIMIT 1
;
```

**Output**
```
"pickup_loc"	"dropoff_loc"	            "tip"
"Astoria"	"Long Island City/Queens Plaza"	 88
```
