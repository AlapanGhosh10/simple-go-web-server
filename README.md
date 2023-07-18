# Build A Simple Web Server With Golang

![Alt text](https://drive.google.com/file/d/1BeWt0OC4llP2l8xnnFg7ovGX0_9PbGOB/view?usp=sharing "10,000 ft view")


/ route → index.html file

/hello route → call hello func 

/form route → form func associated with it → after post request from form.html file

### Steps

go mod init folder-name

- static folder
    - Create index.html
        - Code
            
            ```html
            <html>
                <head>
                    <title>Static Website</title>
                </head>
                <body>
                    <h2>Static Website</h2>
                </body>
            </html>
            ```
            
    - Create form.html
        - Code
            
            ```html
            <html lang="en">
            <head>
                <meta charset="UTF-8">
                <meta name="viewport" content="width=device-width, initial-scale=1.0">
                <title>Document</title>
            </head>
            <body>
                <form method="post" action="/form">
                    <label for="name">Name</label>
                    <input type="text" name="name" value="">
                    <label for="address">Address</label>
                    <input type="text" name="address" value="">
            
                    <input type="submit" value="submit">
                </form>
            </body>
            </html>
            ```
            
- main.go
    
    ### http.FileServer()
    
    `fileServer := http.fileServer(http.Dir("./static"))`
    
    Tells go to check static folder and it automatically searches for index.html
    
    ### http.Handle()
    
    `http.Handle("/", fileServer)`
    
    return index.html
    
    ### http.HandleFunc()
    
    HandleFunc() is used to create handler function for different routes
    
    `http.HandleFunc("/form", formHandler)`
    
    `http.HandleFunc("/hello", helloHandler)`
    
    ### http.ListenAndServer()
    
    func is used to create the server
    
    `http.ListenAndServe(":8080", nil)`
    
    return error if there is an error or returns nil
    
    ### Error Handling
    
    If there is an error ListenAndServer does not return nil
    
    ```go
    if err := http.ListenAndServe(":8080", nil); err != nil {
    		log.Fatal(err)
    }
    ```
    
    ### helloHandler
    
    ```go
    func helloHandler(w http.ResponseWriter, r *http.Request) {
    	if r.URL.Path != "/hello" {
    		http.Error(w, "404 not found", http.StatusNotFound)
    		return
    	}
    	if r.Method != "GET" {
    		http.Error(w, "method is not supported", http.StatusNotFound)
    		return
    	}
    	fmt.Fprintf(w, "Hello!")
    }
    ```
    
    If first if → if the given path is not \hello → then error and return
    
    The \hello route is for GET requests which is default
    If someone explicitly sends a POST request it must give an error
    
    If no error → Fprintf() on the responseWriter object
    
    ### formHandler
    
    - Better Request Handling with switch
        
        ```go
        func formHandler (w http.ResponseWriter, r *http.Request) {
        	**switch r.Method {
        		case "GET":
        			http.ServeFile(w, r, "./static/form.html")
        			return
        		case "POST":
        			if err := r.ParseForm(); err != nil {
        				fmt.Fprintf(w, "ParseForm() err: %v\n", err)
        				return
        			}
        	}**
        	fmt.Fprint(w, "POST request successful\n")
        	name := r.FormValue("name")
        	address := r.FormValue("address")
        	fmt.Fprintf(w, "Name = %v\n", name)
        	fmt.Fprintf(w, "Address = %v\n", address)
        }
        ```
        
    
    ```go
    func formHandler (w http.ResponseWriter, r *http.Request) {
    	if err := r.ParseForm(); err != nil {
    		fmt.Fprintf(w, "ParseForm() err: %v\n", err)
    		return
    	}
    	fmt.Fprint(w, "POST request successful")
    	name := r.FormValue("name")
    	address := r.FormValue("address")
    	fmt.Fprintf(w, "Name = %v\n", name)
    	fmt.Fprintf(w, "Address = %v\n", address)
    }
    ```
    
    ParseForm() → parses the form, if error returns err else nil
    
    extract value from the form with FormValue(”ame”)
    

go build → creates binary executable file

go run main.go
