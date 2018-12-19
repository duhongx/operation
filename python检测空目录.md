## Check if a directory is empty : Method 1
```
import os
def main():    
    dirName = '/home/varun/temp';    
    '''
        Check if a Directory is empty and also check exceptional situations.
    '''    
    if os.path.exists(dirName) and os.path.isdir(dirName):
        if not os.listdir(dirName):
            print("Directory is empty")
        else:    
            print("Directory is not empty")
    else:
        print("Given Directory don't exists")        
        

    '''
        Check if a Directory is empty : Method 1
    '''    
    if len(os.listdir('/home/varun/temp') ) == 0:
        print("Directory is empty")
    else:    
        print("Directory is not empty")
        

    '''
        Check if a Directory is empty : Method 2
    '''    
    if not os.listdir('/home/varun/temp') :
        print("Directory is empty")
    else:    
        print("Directory is not empty")        
 

    print ("****************")
    
if __name__ == '__main__':
    main()
```
