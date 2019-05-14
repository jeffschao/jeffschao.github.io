---
title: "Gimmie A Paper"
excerpt: "DBLP Computer Science Journal and Conference Search<br/><img src='/images/GimmieAPaperWordCloud.png'>"
collection: portfolio
---

Gimme A Paper is a tool to make finding the right research paper easy. Our tool keeps track of topics you find important and even reads abstracts to find what you're looking for.

```python
class SearchView(TemplateView):
    template_name = 'polls/name.html'
    #countOfPapers=cursor.execute("SELECT COUNT(*) FROM papersDBLP")
    #print("THIS IS COUNT OF PAPERS:"+str(countOfPapers))
    #print("THIS IS COUNT OF PAPERS: "+ str(Papersdblp.objects.count())) #wohooo got this to work :D will print count
    #currentCountOfPapers=papersdblp.objects.count()
    #searchResults=[]
    def get(self, request):
        form = SearchForm()
        """
        deleteForm = DeleteRecord()
        insertForm = insertRecord()
        updateRecord = UpdateRecord()
        """
        savePaper = SavePaper()
        countString = "There are " + str(Papersdblp.objects.count()) + " records in this database"
        #countString = "There are " + "NONE" + " records in this database"
    
        args = {'form': form, 'savePaper': savePaper, 'countString': countString} #delete 'form':update from args Sofia
        return render(request, self.template_name,args)

    def post(self, request):
        form = SearchForm(request.POST)
        savePaper = SavePaper(request.POST)
        countString = "There are " + str(Papersdblp.objects.count()) + " records in this database"
        searchResults=[]

        #deleteForm = DeleteRecord(request.POST)        
        #insertForm = insertRecord(request.POST)
        #updateForm = UpdateRecord(request.POST)     

        insert = -1
        


        if savePaper.is_valid():
            #addPaperForm=savePaper(request.POST)
            paperid = savePaper.cleaned_data['PaperID']  #user input  
            if paperid is not None:
                insert = int(paperid);
                if Papersdblp.objects.filter(paper_id=insert).exists(): # if the papaer to be saved is in the database
                  userid = request.user.pk #grab the primary key of the user # line that is not for sure working
                  #print("UserID is "+ str(userid))
                  user=User.objects.filter(pk=userid).first() #grab the user object
                  currentPapers=user.profile.savedPapers #get the current papers for the user form database
                  currentPapers=currentPapers+"/"+str(paperid) #add a new paper
                  user.profile.savedPapers=currentPapers # add a new paper to the database
                  user.save() # save the changes
            #cursor.execute("INSERT INTO users_profile(savedPapers, user_id) VALUES('" + str(paperid) + ",'" + str(userid) + "')")    
        print("Insert is: " + str(insert))
        if insert == -1:
            if form.is_valid():
               authorStr = form.cleaned_data['Authors']
               titleStr = form.cleaned_data['Title']
               yearStr = form.cleaned_data['Year']
               keyword = form.cleaned_data['Keyword']
               print("keyword is "+keyword+str(type(keyword)))
               print ("author string below")
               print (authorStr)

               # saving search results to the database code below
               if request.user.is_authenticated:
                   if authorStr is not None and authorStr !="": #input for this is there
                       print("in the first if")
                       userid = request.user.pk # what is the id of the user
                       user=User.objects.filter(pk=userid).first() #grab the user object
                       print("current author below")
                       currentAuthors=user.profile.userAuthors #get the current authors for the user form database
                       print (currentAuthors)
                        #remove empty strings                      
                       currentAuthors=currentAuthors+"/"+authorStr # add a new author
                       user.profile.userAuthors=currentAuthors # set the new authors 
                       user.save() #save to the database

                   if titleStr is not None and titleStr !="": #input for this is there
                       print("in this print")
                       userid = request.user.pk # what is the id of the user
                       user=User.objects.filter(pk=userid).first() #grab the user object
                       #rint("current author below")
                       currentTitles=user.profile.userTitles #get the current authors for the user form database
                       #print (currentAuthors)
                        #remove empty strings                      
                       currentTitles=currentTitles+"/"+titleStr # add a new author
                       user.profile.userTitles=currentTitles # set the new authors 
                       user.save() #save to the database

                   if keyword is not None and keyword !="": #input for this is there
                       #print("in this print")
                       userid = request.user.pk # what is the id of the user
                       user=User.objects.filter(pk=userid).first() #grab the user object
                       #rint("current author below")
                       currentKeywords=user.profile.userKeywords #get the current authors for the user form database
                       #print (currentAuthors)
                        #remove empty strings                      
                       currentKeywords=currentKeywords+"/"+keyword # add a new author
                       user.profile.userKeywords=currentKeywords # set the new authors 
                       user.save() #save to the database

                       
               if keyword is None or keyword=="": # this part is working :)
                   print("keyword is none")
                   searchResults=Papersdblp.objects.filter(authors__icontains=authorStr, title__icontains=titleStr,year__icontains=yearStr)
               else: # if the user inputted a keyword
                   print("went into else")
                   keywordStr=Keywords.objects.filter(keyword=keyword).first() # does the keyword exist?
                   print(keywordStr)
                   if keywordStr is None: #keyword doesn't exist
                      print("went into the if with keyword is None")
                      insert_into_keywords(keyword)
                      keywordStr=Keywords.objects.filter(keyword=keyword).first()
                      if keywordStr is not None: # if the query is returning something: 
                        keywordStr=keywordStr.papers_id
                        keywordList = keywordStr.split(";")
                        keywordList=filter(None, keywordList)
                        for p in keywordList:
                          #print(" P is "+str(p))
                            pid=int(p)
                            result=Papersdblp.objects.filter(paper_id=pid,authors__icontains=authorStr, title__icontains=titleStr,year__icontains=yearStr).first()
                            if result is not None:
                              searchResults.append(result)

                   else: 
                       keywordStr=keywordStr.papers_id
                       keywordList = keywordStr.split(";")
                       keywordList=filter(None, keywordList)
                       for p in keywordList:
                           #print(" P is "+str(p))
                           pid=int(p)
                           result=Papersdblp.objects.filter(paper_id=pid,authors__icontains=authorStr, title__icontains=titleStr,year__icontains=yearStr).first()
                           if result is not None:
                              searchResults.append(result)
                            

          
       # form = SearchForm()#This loads blank form
           #print("Hello")
           # return redirect('/home/') #this is working now :D
        
        
        
        args = {'form': form, 'savePaper': savePaper, 'searchResults': searchResults, 'countString': countString}#displays text entered
        return render(request, self.template_name, args)
```