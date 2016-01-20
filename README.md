# Operation architecture in elixir. #

## Motivation ##
During development of web applications in Rails and Phoenix we use the *MVC* pattern.
There is no clear idea of where should exactly the application logic should be put in this pattern.
If we wanted to keep our whole application only in those 3 places the most obvious solution would be the *C* standing for controller.
But it creates a problem when trying to share the code between different controllers. That's why we should decouple the application logic to services preferably with as few dependencies as possible.
This is where the idea of **Operations** come to mind.

## Idea problem ##
Same as with all the functions there are 2 types of **Operations** - those with side effects and those without them. 
Because of that we should differentiate the **result** and maybe the **API** for **Operations** depending on their type to make it more predictable what type of operation we are currently using and how to handle it's results.

## Operation examples ##
In every example we will discuss:
  * Operation name
  * Operation result
  * Error handling
  * Testing the operation
  
## Operations without side effects ##

### Example 1 ###
This operation retrieves game and user depending on the socket we provide to it. 

``` elixir
# usage
{game, user} = SocketOperations.get_game_and_user(socket)

# definition
defmodule SprintPoker.SocketOperations do
  def get_game_and_user(socket) do
    "game:" <> game_id = socket.topic
    game = Repo.get!(Game, game_id)
    user = Repo.get!(User, socket.assigns.user_id)
    {game, user}
  end
end
```

#### Operation name ####
The operation is called `SocketOperations.get_game_and_user`.
This name is constructed from `Socket` as the name of resource the operation is for, `Operations` for informing this is an operation, and function name `get_game_and_user` being `get` for informing that it is a **Reader** type operation without side effects and `game_and_user` which explains what the function returns. 
#### Operation result ####
Operation returns a tuple with the resource's after `get` in the function name.
#### Error handling ####
Errors are not handled explicitly in the response, but are raised inside of the operation.
#### Testing ####
The testing of this code is preety straightforward. We create a `socket` with `Game` and `User` that we provide to the function and receive the values from it. 
There are 3 cases we can test here. 1 of them raises an error, 1 returns the resources and 1 returns wrong resources.
