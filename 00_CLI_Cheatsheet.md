# Redis CLI Beginner Cheatsheet

## How to Open Redis CLI

Open your terminal (Git Bash, CMD, or PowerShell) and type:

```
docker exec -it redis-learn redis-cli
```

You'll see this prompt:
```
127.0.0.1:6379>
```

Now you're inside Redis! Type commands and hit Enter.
To exit, type `QUIT` or press Ctrl+C.


---


## Level 1: The Basics (Start Here!)

Try each command one by one. Just type it and press Enter.

```
PING
```
Redis replies: PONG (means it's alive!)

```
SET name Sujit
```
Reply: OK (you stored "Sujit" under the key "name")

```
GET name
```
Reply: "Sujit" (you got it back!)

```
SET age 25
```

```
GET age
```
Reply: "25"

```
SET city Mumbai
```

```
GET city
```

```
GET fakename
```
Reply: (nil) — means this key doesn't exist


---


## Level 2: Overwrite, Delete, Check

```
SET name Sujit
GET name
```
→ "Sujit"

```
SET name Alice
GET name
```
→ "Alice" (it got overwritten! SET always replaces)

```
DEL name
GET name
```
→ (nil) — it's gone!

```
SET color blue
EXISTS color
```
→ 1 (means yes, it exists)

```
EXISTS ghost
```
→ 0 (means no, doesn't exist)


---


## Level 3: Counting (This is Cool!)

```
SET score 0
GET score
```
→ "0"

```
INCR score
```
→ 1 (added 1 automatically!)

```
INCR score
INCR score
INCR score
GET score
```
→ "4"

```
DECR score
```
→ 3 (subtracted 1)

```
INCRBY score 10
```
→ 13 (added 10 at once)

```
DECRBY score 5
```
→ 8 (subtracted 5)

Try this: what happens if you INCR a key that doesn't exist?

```
DEL newcounter
INCR newcounter
```
→ 1 (it created it and started from 0!)


---


## Level 4: Set Multiple, Get Multiple

```
MSET fruit apple drink water food pizza
```
→ OK (set 3 keys at once!)

```
MGET fruit drink food
```
→ 1) "apple"  2) "water"  3) "pizza"


---


## Level 5: Keys with Expiry (Self-Deleting Keys!)

```
SET session hello EX 10
```
This key will DELETE ITSELF after 10 seconds!

```
GET session
```
→ "hello" (if you're fast enough)

```
TTL session
```
→ shows seconds remaining (like a countdown)

Wait 10 seconds, then:
```
GET session
```
→ (nil) — it's gone!

```
TTL session
```
→ -2 (means key doesn't exist anymore)

TTL meanings:
- positive number = seconds left
- -1 = key exists but no expiry (permanent)
- -2 = key doesn't exist


---


## Level 6: Lists (Like a To-Do List)

```
RPUSH tasks "buy groceries"
RPUSH tasks "clean room"
RPUSH tasks "learn redis"
```
(RPUSH = add to the Right/end)

```
LRANGE tasks 0 -1
```
→ shows all items (0 = start, -1 = end)

```
LPUSH tasks "wake up"
```
(LPUSH = add to the Left/beginning)

```
LRANGE tasks 0 -1
```
→ "wake up" is now first!

```
LPOP tasks
```
→ removes and shows the first item

```
RPOP tasks
```
→ removes and shows the last item

```
LLEN tasks
```
→ how many items left


---


## Level 7: Sets (Bag of Unique Items)

```
SADD fruits apple banana cherry apple
```
→ 3 (only 3 added — apple was duplicate!)

```
SMEMBERS fruits
```
→ shows all items (order may vary)

```
SISMEMBER fruits apple
```
→ 1 (yes, it's in the set)

```
SISMEMBER fruits mango
```
→ 0 (no, it's not)

```
SCARD fruits
```
→ 3 (count of items)

Now make another set:
```
SADD veggies banana carrot pea
```

Find what's in BOTH sets:
```
SINTER fruits veggies
```
→ "banana" (the only common item!)

Find everything from both sets combined:
```
SUNION fruits veggies
```


---


## Level 8: Hashes (Like a Mini Profile Card)

Instead of:
```
SET user:1:name Sujit
SET user:1:age 25
SET user:1:city Mumbai
```

Use ONE hash:
```
HSET user:1 name Sujit age 25 city Mumbai
```

```
HGET user:1 name
```
→ "Sujit"

```
HGET user:1 age
```
→ "25"

```
HGETALL user:1
```
→ shows ALL fields and values

```
HINCRBY user:1 age 1
```
→ 26 (happy birthday!)

```
HDEL user:1 city
HGETALL user:1
```
→ city is gone, name and age remain


---


## Level 9: Sorted Sets (Leaderboard!)

```
ZADD leaderboard 100 alice
ZADD leaderboard 85 bob
ZADD leaderboard 92 charlie
```

Show rankings (lowest to highest):
```
ZRANGE leaderboard 0 -1 WITHSCORES
```

Show rankings (highest first — like a real leaderboard):
```
ZREVRANGE leaderboard 0 -1 WITHSCORES
```

What's alice's score?
```
ZSCORE leaderboard alice
```

What's alice's rank? (0 = #1)
```
ZREVRANK leaderboard alice
```

Give bob 20 more points:
```
ZINCRBY leaderboard 20 bob
```

Check the leaderboard again:
```
ZREVRANGE leaderboard 0 -1 WITHSCORES
```
→ bob moved up!


---


## Level 10: Useful Commands

See ALL keys:
```
KEYS *
```

See keys matching a pattern:
```
KEYS user:*
```

What type is a key?
```
TYPE tasks
TYPE fruits
TYPE user:1
TYPE leaderboard
```

How many keys total?
```
DBSIZE
```

Delete EVERYTHING (careful!):
```
FLUSHDB
```

Server info:
```
INFO server
```


---


## Quick Reference Card

```
Strings:     SET, GET, DEL, INCR, DECR, MSET, MGET, EXISTS
Expiry:      SET key val EX seconds, TTL key, EXPIRE key seconds
Lists:       RPUSH, LPUSH, LRANGE, LPOP, RPOP, LLEN
Sets:        SADD, SMEMBERS, SISMEMBER, SCARD, SINTER, SUNION
Hashes:      HSET, HGET, HGETALL, HDEL, HINCRBY
Sorted Sets: ZADD, ZRANGE, ZREVRANGE, ZSCORE, ZRANK, ZINCRBY
General:     KEYS, TYPE, DBSIZE, FLUSHDB, PING, QUIT
```


---


## Tips

- Commands are NOT case-sensitive (SET = set = Set)
- Keys ARE case-sensitive ("Name" and "name" are different keys)
- Use quotes for values with spaces: SET msg "hello world"
- When done playing, type QUIT to exit
- To start fresh, type FLUSHDB (deletes all keys)
- Have fun breaking things — that's how you learn!
