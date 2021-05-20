The primary objective of interaction-oriented architecture is to separate the interaction of user from data abstraction and business data processing. 

MVC2
# Input is routed to the Controller. 
# View is stateless
# A Controller can choose to render one of many Views. 
# The View has no knowledge of its Controller. 
# The main difference between Passive Model and Active Model is that in Active Model the Model notifies the View when the Model is changed by Controller. 

MVP
# Input is routed to the View. 
# The Presenter updates the View usually in response to events raised by the View. 
# State is effectively stored in the View. 

MVVM
# Input is routed to the View. 
# The View knows about nothing but the ViewModel. 
# The ViewModel knows about nothing but the Model. 
# The View gets its data from the ViewModel, not the Model itself. This is generally accomplished by data binding.  
# State and UI logic exist in the ViewModel. 
# The ViewModel can be thought of as an abstract representation of the UI.
