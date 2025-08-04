# Early Voting App v1 - web

## Challenge

Hey everyone! We're Beta testing this new web application we're going to deploy to our voters to potentially replace our mail in voting system.

Ok fine, maybe its more in the alpha stage, but we're quite excited about this one! Feel free to give it a go with the example user.

We've implemented some logic for when a candidate wins by getting all the votes, but... that shouldn't be possible with only one user anyways.

## Solve

Visiting the website we are provided some some test login credentials to authenticate to the service. There's `Edit Profile` and `View Profile` buttons, and if we click either one, a `user_id` parameter appears in the URL.

The value of the parameter is simply `2`. Changing this to `1` or `3` allows us to access the accounts of other users in a classic Insecure Direct Object Reference (IDOR) vulnerbaility.

vote.png

Changing into each user account at the `Edit Profile` page we can place all votes on a singular candidate and retrieve the flag:

`secedu{w0aw_dud3_th@t5_iD0r_4_ya_aa9s8}`