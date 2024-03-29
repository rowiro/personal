#iOS notes

### how to implement async calls 
```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    dispatch_async(kBgQueue, ^{
        NSData* data = [NSData dataWithContentsOfURL: 
          kLatestKivaLoansURL];
        [self performSelectorOnMainThread:@selector(fetchedData:) 
          withObject:data waitUntilDone:YES];
    });
}
- (void)fetchedData:(NSData *)responseData {
    //parse out the json data
    NSError* error;
    NSDictionary* json = [NSJSONSerialization 
        JSONObjectWithData:responseData //1
 
        options:kNilOptions 
        error:&error];
 
    NSArray* latestLoans = [json objectForKey:@"loans"]; //2
    // 1) Get the latest loan
    NSDictionary* loan = [latestLoans objectAtIndex:0];
     
    // 2) Get the funded amount and loan amount
    NSNumber* fundedAmount = [loan objectForKey:@"funded_amount"];
    NSNumber* loanAmount = [loan objectForKey:@"loan_amount"];
    float outstandingAmount = [loanAmount floatValue] - [fundedAmount floatValue];
     
    // 3) Set the label appropriately
    humanReadble.text = [NSString stringWithFormat:@"Latest loan: %@ 
      from %@ needs another $%.2f to pursue their entrepreneural dream",
      [loan objectForKey:@"name"],
      [(NSDictionary*)[loan objectForKey:@"location"] 
        objectForKey:@"country"],
        outstandingAmount];
}
```

[Reference](http://www.raywenderlich.com/5492)


### where set table view cell separators:
tableview-> attribule inspector -> separator -> single line

### change separator line color: 
```objective-c
self.tableView.separatorColor = [UIColor whiteColor];
```

###how to change tableview background color
```objective-c
[self.tableView setBackgroundColor:[UIColor yellowColor]];
```

### how to change tableview background image
```objective-c
    self.tableView.backgroundView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"table_background.png"]];
```

### customize section head of table view
```objective-c
-(UIView *) tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section {
    static NSString *CellIdentifier = @"SectionHeader";
    UITableViewCell *headerView = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (headerView == nil){
        [NSException raise:@"headerView == nil.." format:@"No cells with matching CellIdentifier loaded from your storyboard"];
    }
    return headerView;
}
```
### height of section head of table view 
```objective-c
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section
    {
        return 40.0;
    }
```
### how to convert uicolor with hex color
[reference](http://uicolor.org/)
I note previous reference link will not work properly in 64bit iOS device, so I use following function now:
```objective-c
-(UIColor *)colorFromHex:(NSString *)hex {
    unsigned int c;
    if ([hex characterAtIndex:0] == '#') {
        [[NSScanner scannerWithString:[hex substringFromIndex:1]] scanHexInt:&c];
    } else {
        [[NSScanner scannerWithString:hex] scanHexInt:&c];
    }
    return [UIColor colorWithRed:((c & 0xff0000) >> 16)/255.0
                           green:((c & 0xff00) >> 8)/255.0
                            blue:(c & 0xff)/255.0 alpha:1.0];
}
```
### hide status bar:
```objective-c
- (BOOL)prefersStatusBarHidden {
    return YES;
}
```
### hide navagition
#### To hide Navigation bar :
```objective-c
[self.navigationController setNavigationBarHidden:YES animated:YES];
```
#### To show Navigation bar :
```objective-c
[self.navigationController setNavigationBarHidden:NO animated:YES];
```
### table view cell select and push to another view and pass some parameters
```objective-c
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath 
{
     //add code of another view controller
}
```
[Reference](http://stackoverflow.com/questions/11327052/push-to-next-view-by-clicking-on-table-cell-with-using-storyboard)
### how to hide tableviewcell selection arrow
```objective-c
cell.accessoryType = UITableViewCellAccessoryNone;
```
### navigation view controller, go to previous view
```objective-c
[self.navigationController popViewControllerAnimated:TRUE];
```
### direct query of URL string
```objective-c
### url query string:
NSURL *url = [NSURL URLWithString:@"https://blockchain.info/tobtc?currency=USD&value=1"];
    NSString *webData= [NSString stringWithContentsOfURL:url];
    NSLog(@"%@",webData);
```
### add actions for the text field when user typing 
```objective-c
 [_btcField addTarget:self action:@selector(typeBTC:) forControlEvents:UIControlEventEditingChanged];
```
### how to dismiss decimal pad
```objective-c
### dismiss decimal pad:
 UITapGestureRecognizer *tapRecgonizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tap:)];
    [self.view addGestureRecognizer:tapRecgonizer];
- (void) tap: (UIGestureRecognizer* ) gr
{
    NSLog(@"YES, YOU JUST TAPPED");
    [self.view endEditing:YES];
}
```
[Reference](http://code-and-coffee.blogspot.sg/2013/01/how-to-dissmis-decimal-pad-keyboard.html?showComment=1384935807857)

### how to implement the normal "swipe left for settings side bar" ?
[Reference](http://www.appcoda.com/ios-programming-sidebar-navigation-menu/)
### simple drop down list:
[Reference](http://www.edumobile.org/iphone/iphone-programming-tutorials/a-simple-drop-down-list-for-iphone/)
### take screenshot on simulator
edit->copy screen -> open preview, cmd+ s to save
### how to customize left/right swipe segue
```objective-c
//in view did load//
{
    UISwipeGestureRecognizer *rightRecognizer = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(rightSwipeHandle:)];
    rightRecognizer.direction = UISwipeGestureRecognizerDirectionRight;
    [rightRecognizer setNumberOfTouchesRequired:1];
    [self.view addGestureRecognizer:rightRecognizer];
    UISwipeGestureRecognizer *leftRecognizer = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(leftSwipeHandle:)];
    leftRecognizer.direction = UISwipeGestureRecognizerDirectionLeft;
    [leftRecognizer setNumberOfTouchesRequired:1];
    [self.view addGestureRecognizer:leftRecognizer];
    }

    - (void)rightSwipeHandle:(UISwipeGestureRecognizer*)gestureRecognizer
{
    [self.navigationController popViewControllerAnimated:YES];
}

- (void)leftSwipeHandle:(UISwipeGestureRecognizer*)gestureRecognizer
{
    [self performSegueWithIdentifier:@"forward" sender:self];
}
```
### how to zoom in/out emulator size?
cmd + 1/2/3
### calling a method for every 60 seconds
```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
	// Do any additional setup after loading the view, typically from a nib.
    [NSTimer scheduledTimerWithTimeInterval:60.0
                                     target:self
                                   selector:@selector(someMethod)
                                   userInfo:nil
                                    repeats:YES];
}

- (void) someMethod {
    NSLog(@"hello I am called");
}
```
### how to set view layout compatible for both 3.5inch and 4inch screen
1. auto-layout
2. constrains

### how to distribute your app wirelessly for beta user
main idea: create a new distribute license with allowed iphone udid inside it, compile the code and siging with the license. 
[Reference](http://help.apple.com/iosdeployment-apps/mac/1.1/#app43ad871e)

### how to dismiss alert view after 5 seconds if user did not click "OK"
```objective-c
//method1
[self performSelector:@selector(hideAlertView:) withObject:staticalertView afterDelay:5]
//method2
dispatch_async(dispatch_get_main_queue(), ^{
        [self performSelector:@selector(hideAlertView:) withObject:staticalertView afterDelay:5];
    });
```

### add ios button dynamically and associated them with ibactions:
```objective-c
UIButton *myButton = [[UIButton alloc] init...];
[myButton addTarget:something action:@selector(myAction) forControlEvents:UIControlEventTouchUpInside];
```
[Reference](http://stackoverflow.com/questions/2370031/programatically-generating-uibuttons-and-associate-those-with-ibaction)

### change project name in Xcode, but its name just does not change
Please search for whole project of CFBundleDisplayName, maybe it is hardcoded somewhere.

### when add segue for modal view -> navigation controller view, the navigation bar disappears.
Please connect segue from modal view to navigation controller, not the navigation controller root view


### why UILabel change frame can not work
Plese make sure disable auto-layout 
```objective-c
CGSize maxSize = CGSizeMake(_mainLabel.frame.size.width, MAXFLOAT);
NSString * testString = @"here adlk dal faskdfjl jasdlfjslkadflksadfkl jsakdfsadf dsfdsf asdfasdf asdfasdfasdf asdfasdf sadf sadf ";
CGRect labelRect = [testString boundingRectWithSize:maxSize options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName:_mainLabel.font} context:nil];
CGRect frame = _mainLabel.frame;
frame.size.height = labelRect.size.height;
```
### how to access UIButton in UITableViewCell
set UIButton with tag
```objective-c
UIButton *button2 = (UIButton *)[[cell contentView] viewWithTag:2];
```

### how to parse json data without knowing their keys? 
```objective-c
    NSString *prefix=@"http://ec2-54-251-248-32.ap-southeast-1.compute.amazonaws.com/backup/db/samquery1.php?userid=";
    NSInteger uid = 100;     //let's say user id is 111
    NSString *postfix = [NSString stringWithFormat:@"%ld", (long)uid];
    NSString *qstring = [ prefix stringByAppendingString:postfix];
    NSURL *url=[NSURL URLWithString:qstring];
    NSData *data=[NSData dataWithContentsOfURL:url];
    
    NSError *error=nil;
    NSArray *jsonArray =[NSJSONSerialization JSONObjectWithData:data options:
                         NSJSONReadingMutableContainers error:&error];
    if (!jsonArray) {
        NSLog(@"Error parsing JSON: %@", error);
    } else {
        for(NSDictionary *item in jsonArray) {
            //NSLog(@"Item: %@", item);
            //NSString *negemo = [item objectForKey:@"negemo"];
            //NSLog(@"negative emo is %@", negemo);
            
            NSLog(@"=====start of row=====\n");
            NSArray *keys = [item allKeys];
            for (id key in keys)
            {
                id aValue = [item objectForKey:key];
                NSLog(@"key is %@ and value is %@",key,aValue);
            }
            NSLog(@"=====end of row=====\n");
        }
    }
```

### how to change tableview cell alpha value:
```objective-c
cell.contentView.alpha = 0.5;
```
### how to add different separator for dif tableview cells
in viewdidload add:
```objective-c
self.tableView.separatorColor = [UIColor clearColor];
```
in cellForRowAtIndexPath add
```objective-c
CALayer *separator = [CALayer layer];
        separator.backgroundColor = [UIColor whiteColor].CGColor;
        separator.frame = CGRectMake(0, -1, self.view.frame.size.width, 1);
        [cell.layer addSublayer:separator];
```
### how to write to *.plist file
```objective-c
- (NSApplicationTerminateReply)applicationShouldTerminate:(NSApplication *)sender {
    NSString *error;
    NSString *rootPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
    NSString *plistPath = [rootPath stringByAppendingPathComponent:@"Data.plist"];
    NSDictionary *plistDict = [NSDictionary dictionaryWithObjects:
            [NSArray arrayWithObjects: personName, phoneNumbers, nil]
            forKeys:[NSArray arrayWithObjects: @"Name", @"Phones", nil]];
    NSData *plistData = [NSPropertyListSerialization dataFromPropertyList:plistDict
                            format:NSPropertyListXMLFormat_v1_0
                            errorDescription:&error];
    if(plistData) {
        [plistData writeToFile:plistPath atomically:YES];
    }
    else {
        NSLog(error);
        [error release];
    }
    return NSTerminateNow;
}
```

### how to change navigation bar tint color
```objective-c
self.navigationController.navigationBar.barTintColor = [UIColor colorWithRed:26/255.0f green:188/255.0f blue:156/255.0f alpha:1.0f];
    self.navigationController.navigationBar.tintColor = [UIColor whiteColor];
    [self.navigationController.navigationBar setTitleTextAttributes:@{NSForegroundColorAttributeName : [UIColor whiteColor]}];
    self.navigationController.navigationBar.translucent = NO;
```
### particular cell is not selectable
```objective-c
    if (indexPath.row == 0)
    {
        // we decide here that first cell in the table is not selectable (it's just an indicator)
        cell.selectionStyle = UITableViewCellSelectionStyleNone;
    }
```
### how to delete a *.plist file programmatically
```objective-c
if(![[NSFileManager defaultManager] removeItemAtPath:documentPlistPath error:&error])
    {
        //TODO: Handle/Log error
        NSLog(@"got error");
    }
    else {
        NSLog(@"delete successfuly");
    }
```
### how to copy bundle plist(which is read only) to document plist file(which is writable)?
```objective-c
NSFileManager *fileManager = [NSFileManager defaultManager];
    NSError *error;
    NSArray *documentPaths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *documentsDirectory = [documentPaths objectAtIndex:0];
    NSString *documentPlistPath = [documentsDirectory stringByAppendingPathComponent:@"messages.plist"];
    NSString *bundlePath = [[NSBundle mainBundle] bundlePath];
    NSString *bundlePlistPath = [bundlePath stringByAppendingPathComponent:@"messages.plist"];
    
    BOOL success = [fileManager copyItemAtPath:bundlePlistPath toPath:documentPlistPath error:&error];
    if (success) {
        NSArray *array = [NSArray arrayWithContentsOfFile:documentPlistPath];
        NSLog(@"success: after copied the plist file is %@", array);
    }
    else {
        NSArray *array = [NSArray arrayWithContentsOfFile:documentPlistPath];
        NSLog(@"fail: after copied the plist file is %@", array);
    }
```

### select tableview cell and perform segue with some parameters:
```objective-c
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
	[self performSegueWithIdentifier:@"editEvent" sender:[self.tableView cellForRowAtIndexPath:indexPath]];
}
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
{
    UITableViewCell *cell = sender;
    // Make sure your segue name in storyboard is the same as this line
    NSLog(@"inside prepare for segue");
    if ([[segue identifier] isEqualToString:@"editEvent"])
    {
        //((BTCSegue *)segue).animationType = 0 ;
        // Get reference to the destination view controller
        llEditLogViewController *editViewController = [segue destinationViewController];
        NSIndexPath *indexPath = [self.tableView indexPathForCell:cell];
        NSInteger rowSelected = [indexPath row];
        editViewController.rowSelected = rowSelected;
        NSLog(@"prepare for segue, and row is %ld",(long)rowSelected);
    }
}

### how to parse standard time string and format it (eg: 2014-01-02 10:10:10)?
```objective-c
formatter.dateFormat = @"yyyy-MM-dd HH:mm:ss";
formatter.timeZone = [NSTimeZone timeZoneWithAbbreviation:@"UTC"];
NSDate *date = [formatter dateFromString:[tmpDict objectForKey:@"posttime"]];
NSDateFormatter *formatter2 = [[NSDateFormatter alloc] init];
formatter2.dateFormat = @"dd-MMM HH:mm";
NSString *postTime = [formatter2 stringFromDate:date];
NSLog(@" post time is %@", postTime);
```

### how to add activityIndicator during webview loading?
```objective-c
- (void)viewDidLoad
{
    [super viewDidLoad];
    NSString * urlString = @"......";
    [_postWebView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:urlString]]];
    _postWebView.delegate = self;
}

- (void)webViewDidStartLoad:(UIWebView *)webView{
    [self.postProgress setHidden:NO];
    [self.postProgress startAnimating];
    
}

- (void)webViewDidFinishLoad:(UIWebView *)webView{
    [self.postProgress stopAnimating];
    [self.postProgress setHidden:YES];
}
```

### how to rename delete table view cell "Delete"?
```objective-c
//let's say we want to rename it to close
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath {
    return @"Close";
}
```

### iOS push notification implementation
[reference1](http://blog.csdn.net/daydreamingboy/article/details/7977098)
[reference2](http://www.raywenderlich.com/32960/apple-push-notification-services-in-ios-6-tutorial-part-1)


### how to add app walkthrough? 
1. detect whether the user is first time launching app or not [Reference](http://stackoverflow.com/a/13335636/874585)
2. perform segue to walkthrough view if it is first time user [Refenrence](http://www.appcoda.com/uipageviewcontroller-storyboard-tutorial/)

### get URL source code:
```objective-c
NSURL *mobileurl = [NSURL URLWithString:@"http://campusbus.ntu.edu.sg/ntubus/index.php/m/main"];
    NSError* error = nil;
    NSString* text = [NSString stringWithContentsOfURL:mobileurl encoding:NSASCIIStringEncoding error:&error];
    if( text )
    {
        NSLog(@"Text=%@", text);
    }
    else
    {
        NSLog(@"Error = %@", error);
    }
```

### Delete the extra separator of UITableView in iOS 7

```objective-c
- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section
{        
    return [UIView new];
    // If you are not using ARC:
    // return [[UIView new] autorelease];
}
```

### how to add side bar to the view
[reference](http://www.appcoda.com/ios-programming-sidebar-navigation-menu/)

problem: swipe to delete tableview cell seems not working any more.
https://github.com/John-Lluch/SWRevealViewController/issues/104

### long press to re-ordering tableview cells
[reference](https://github.com/bvogelzang/BVReorderTableView)

### how to add line to UIView?
```objective-c
UIView * separator = [[UIView alloc] initWithFrame:CGRectMake(20, 104, 320, (1.0 / [UIScreen mainScreen].scale) / 2)];
    separator.backgroundColor = [UIColor colorWithWhite:0.7 alpha:1];
    [self.view addSubview:separator];
```
#### how to make the line exactly 1px for both normal iOS device as well as retina display device?
```objective-c
CGFloat scaleOfMainScreen = [UIScreen mainScreen].scale;
    CGFloat alwaysOnePixelInPointUnits = 1.0/scaleOfMainScreen;
    
    UIView * separator = [[UIView alloc] initWithFrame:CGRectMake(20, 40, 300, alwaysOnePixelInPointUnits)];
    separator.backgroundColor = [UIColor colorWithWhite:1 alpha:0.2];
    [self.view addSubview:separator];
```    
### dismiss keyboard when user taps other area:
```objective-c
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    for (UIView * txt in self.view.subviews){
        if ([txt isKindOfClass:[UITextField class]] && [txt isFirstResponder]) {
            [txt resignFirstResponder];
        }
    }
}

/*OR*/

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    [self.view endEditing:YES];    
}
```
[reference](http://stackoverflow.com/questions/18756196/how-to-dismiss-keyboard-when-user-tap-other-area-outside-textfield)

### textfield werid behavior
[ref](http://stackoverflow.com/questions/14252470/how-to-dismiss-keyboard-on-second-time-selecting-first-textfield)
[ref2](http://stackoverflow.com/questions/10051402/keyboard-not-responding-to-resignfirstresponder)


### how to implement movable/reorder tableview cells?
[ref1](https://github.com/yonat/EditableList)


### NSArray and NSMutableArray
```objective-c
NSArray* arr = [[NSArray alloc]initWithObjects:@"work",@"email",@"dreamon",@"aaa",@"bbb",@"ccc", nil];
NSMutableArray *randomSelection =  [[NSMutableArray alloc]init];
[randomSelection addObject:@"string1"];
```

### how to implement a dropdown list
[example1](https://github.com/nmattisson/DropdownMenu)
[example2](https://github.com/romaonthego/REMenu)
[example3](https://github.com/BijeshNair/NIDropDown)


### how to set view background image?

```objective-c
self.view.backgroundColor = [UIColor colorWithPatternImage:[UIImage imageNamed:@"randomgrey.png"]];
```

#### how to stretch the image if size not fit?
```objective-c
    UIGraphicsBeginImageContext(self.view.frame.size);
    [[UIImage imageNamed:@"chat_blur_default9"] drawInRect:self.view.bounds];
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    self.view.backgroundColor = [UIColor colorWithPatternImage:image];
```    

### swipe to delete table view cell?
```objective-c
- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath
{
    // Return YES - we will be able to delete all rows
    return YES;
}

- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath
{
    // Perform the real delete action here. Note: you may need to check editing style
    //   if you do not perform delete only.
    NSLog(@"Deleted row.");
}
```


### how to get iPhone simulator directory?
```
dirPaths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    docsDir = dirPaths[0];
    NSLog(@"%@",docsDir);
```    

### how to view sqlite visually on mac?
[try this](https://itunes.apple.com/us/app/sqlite-professional-read-only/id635299994?mt=12)


### how to use sqlite?
(follow this tutorial](http://www.techotopia.com/index.php/An_Example_SQLite_based_iOS_7_Application)


### how to add iOS default share sheet?
```objective-c

    NSString *text = @"hello world";
            NSURL *url = [NSURL URLWithString:@"http://airbob.github.io/lifeNumber"];
            //UIImage *image = [UIImage imageNamed:@"roadfire-icon-square-200"];
            
            UIActivityViewController *controller =
            [[UIActivityViewController alloc]
             initWithActivityItems:@[text, url]
             applicationActivities:nil];
            
            
            controller.excludedActivityTypes = @[UIActivityTypePrint,
                                                 UIActivityTypeCopyToPasteboard,
                                                 UIActivityTypeAssignToContact,
                                                 UIActivityTypeSaveToCameraRoll,
                                                 UIActivityTypeAddToReadingList,
                                                 UIActivityTypeAirDrop];
            [self presentViewController:controller animated:YES completion:nil];
```

### how to generate months array?

```objective-c
NSArray *monthlySymbols = [[[NSDateFormatter alloc] init] shortMonthSymbols];
```

### how to iterate date from one year ago to today?
```objective-c 
    NSDate *now = [NSDate date];
    NSDate *seventyDaysAgo = [now dateByAddingTimeInterval:-356*24*60*60];
    //NSLog(@"70 days ago: %@", seventyDaysAgo);
    
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
	[dateFormatter setDateFormat:@"yyyy-MM-dd"];
	//NSString *MyString = [dateFormatter stringFromDate:seventyDaysAgo];
    
    
    
    NSCalendar *calendar = [NSCalendar currentCalendar];
    NSDateComponents *oneDay = [[NSDateComponents alloc] init];
    [oneDay setDay: 1];
    
    for (NSDate* date = seventyDaysAgo; [date compare: now] <= 0;
         date = [calendar dateByAddingComponents: oneDay
                                          toDate: date
                                         options: 0] ) {
             //NSLog( @"%@ in [%@,%@]", date, sevenDaysAgo, now );
             NSLog(@"date is %@", [dateFormatter stringFromDate:date]);
         }
```

### how to remove file in document path
```objective-c
- (void)removeFile:(NSString *)fileName
{
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
    
    NSString *filePath = [documentsPath stringByAppendingPathComponent:fileName];
    NSError *error;
    BOOL success = [fileManager removeItemAtPath:filePath error:&error];
    if (success) {
        NSLog(@"removed");
    }
    else
    {
        NSLog(@"Could not delete file -:%@ ",[error localizedDescription]);
    }
}
```

### how to set keyboard return action?
Adopt the UITextFieldDelegate method and set yourself as the delegate. Implement textFieldShouldReturn: method like this,
```objective-c
- (BOOL)textFieldShouldReturn:(UITextField *)textField {
    [textField resignFirstResponder];
    return YES;
}
```

### how to add hyperlink to button click?
```objective-c 
-(void) buttonpressed:(UIButton *)sender {
    NSString* launchUrl = @"http://apple.com/";
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString: launchUrl]];
}

```

## sprite kit

### how to add a node with image name

```objective-c
CGPoint location = [touch locationInNode:self];
        
        SKSpriteNode *sprite = [SKSpriteNode spriteNodeWithImageNamed:@"Spaceship"];
        
        sprite.position = location;
        
        SKAction *action = [SKAction rotateByAngle:M_PI duration:1];
        
        [sprite runAction:[SKAction repeatActionForever:action]];
        
        [self addChild:sprite];

```
### how to detect general SKNode touched?
```objective-c
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    UITouch *touch = [touches anyObject];
    CGPoint touchLocation = [touch locationInNode:self];
    SKNode *touchedNode = [self nodeAtPoint:touchLocation];
    
    NSLog(@"touchLocation x: %f and y: %f", touchLocation.x, touchLocation.y);
    
    if (touchedNode != self) {
        NSLog(@"Removed from parent.");
        //[touchedNode removeFromParent];
        UIViewController *vc = self.view.window.rootViewController;
        [vc performSegueWithIdentifier:@"toSettings" sender:nil];
    }
}

```

### how to detect certain SKNode touched?

```objective-c
//assign the name property when add the node:
        //      second label
        SKLabelNode *myLabel2 = [SKLabelNode labelNodeWithFontNamed:@"Chalkduster"];
        //        myLabel2.userInteractionEnabled = YES;
        myLabel2.name = @"nodename";
        myLabel2.text = @"Hello, World!";
        myLabel2.fontSize = 30;
        myLabel2.position = CGPointMake(100, 100);
        [self addChild:myLabel2];
        
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    UITouch *touch = [touches anyObject];
    CGPoint touchLocation = [touch locationInNode:self];
    SKNode *touchedNode = [self nodeAtPoint:touchLocation];
    
    NSLog(@"touchLocation x: %f and y: %f", touchLocation.x, touchLocation.y);
    
    if ([touchedNode.name isEqualToString:@"nodename"]) {
        NSLog(@"Removed from parent.");
        //[touchedNode removeFromParent];
        UIViewController *vc = self.view.window.rootViewController;
        [vc performSegueWithIdentifier:@"toSettings" sender:nil];
    }
}
```
## iAd
### how to add iAd:
case 1: juse add a normal iAd banner at bottom of view:
step 1: build phase add linked framework (iAds) <br/>
step 2: add ```#import <iAd/iAd.h>``` in *.h file, in *.m viewdidLoad add ``` self.canDisplayBannerAds = YES;```<br>
[Reference](http://www.youtube.com/watch?v=fP2ijcXbCz4)
[Reference2](http://stackoverflow.com/questions/19717446/how-to-have-an-in-app-purchase-to-remove-iads)

case 2: add iAd banner in the view
step1: add framework <br>
step2: import iAd and add ADBannerViewDelegate <br>
step3: add banner to view, drag and link IBOutlet<br>
step4: add following methods:

```objective-c
-(void)bannerView:(ADBannerView *)banner
didFailToReceiveAdWithError:(NSError *)error{
    NSLog(@"Error in Loading Banner!");
}

-(void)bannerViewDidLoadAd:(ADBannerView *)banner{
    NSLog(@"iAd banner Loaded Successfully!");
}
-(void)bannerViewWillLoadAd:(ADBannerView *)banner{
    NSLog(@"iAd Banner will load!");
}
-(void)bannerViewActionDidFinish:(ADBannerView *)banner{
    NSLog(@"iAd Banner did finish");
    
}
```

### how to use blocks?

[ref1](http://rypress.com/tutorials/objective-c/blocks.html)
[ref2](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/ProgrammingWithObjectiveC.pdf)
[ref3](http://stackoverflow.com/questions/19171206/save-a-completion-handler-as-an-object)


## game center:
step1: add GameKit framework<br>
step2: import ```#import <GameKit/GameKit.h>``` and add ```GKGameCenterControllerDelegate```
step3: add authenticate function:
```objective-c
#pragma mark - game center functions
[self authenticateLocalPlayer];
-(void)authenticateLocalPlayer{
    GKLocalPlayer *localPlayer = [GKLocalPlayer localPlayer];
    
    localPlayer.authenticateHandler = ^(UIViewController *viewController, NSError *error){
        if (viewController != nil) {
            [self presentViewController:viewController animated:YES completion:nil];
        }
        else{
            if ([GKLocalPlayer localPlayer].authenticated) {
                _gameCenterEnabled = YES;
                
                // Get the default leaderboard identifier.
                [[GKLocalPlayer localPlayer] loadDefaultLeaderboardIdentifierWithCompletionHandler:^(NSString *leaderboardIdentifier, NSError *error) {
                    
                    if (error != nil) {
                        NSLog(@"%@", [error localizedDescription]);
                    }
                    else{
                        _leaderboardIdentifier = leaderboardIdentifier;
                        NSLog(@"leader board identifier name is %@", _leaderboardIdentifier);
                    }
                }];
            }
            
            else{
                _gameCenterEnabled = NO;
            }
        }
    };
}
```
### how to integrate game center in your app?
[Ref1](http://www.appcoda.com/ios-game-kit-framework/)
[Ref2](http://www.raywenderlich.com/3276/game-center-tutorial-for-ios-how-to-make-a-simple-multiplayer-game-part-12)


### how to set navigation bar title with a text field?
```objective-c
    UITextField *textField = [[UITextField alloc]initWithFrame:CGRectMake(0, 0, 200, 22)];
        textField.text = @"Insert Title Here";
    textField.font = [UIFont boldSystemFontOfSize:19];
    textField.textColor = [UIColor whiteColor];
    textField.textAlignment = NSTextAlignmentCenter;
    textField.delegate = self;
    self.navigationItem.titleView = textField;
```

### how to set user default and sync it?
```objective-c
//when set 
[[NSUserDefaults standardUserDefaults] setObject:textField.text forKey:@"mainTitle"];
[[NSUserDefaults standardUserDefaults] synchronize];
//when get
NSString *tempTitle = [[NSUserDefaults standardUserDefaults] objectForKey:@"mainTitle"];
```

### how to detect when hit return of textfield?
step1: add UITextFieldDelegate
step2: add 
```objective-c
-(BOOL) textFieldShouldReturn:(UITextField *)textField{
    NSLog(@"text field is %@", textField.text);
    [textField resignFirstResponder];
    return YES;
}
```

### how to set uibutton round corner?
```objective-c
    self.button.layer.masksToBounds = YES;
    self.button.layer.cornerRadius = 5.0;
```

### how to add border with color for UIButton?
```objective-c
    [[self.button layer] setBorderWidth:2.0f];
    [[self.button layer] setBorderColor:[UIColor whiteColor].CGColor];
```

### how to implement long press gesture to re order the tableview items?
[reference](http://www.raywenderlich.com/63089/cookbook-moving-table-view-cells-with-a-long-press-gesture)

### Make circular image and/or add border?
```objective-c
//circle
self.profileImageView.layer.cornerRadius = self.profileImageView.frame.size.width / 2;
self.profileImageView.clipsToBounds = YES;
//border
self.profileImageView.layer.borderWidth = 3.0f;
self.profileImageView.layer.borderColor = [UIColor whiteColor].CGColor;
```

### Change value of segmentedcontrol by swipegesture?
```objective-c
NSInteger index = self.segmentedControl.selectedSegmentIndex;
if ([swipeRecogniser direction] == UISwipeGestureRecognizerDirectionLeft) {
    --index;
} else if ([swipeRecogniser direction] == UISwipeGestureRecognizerDirectionRight) {
    ++index;
}

if (0 <= index && index < self.segmentedControl.numberOfSegments) {
    self.segmentedControl.selectedSegmentIndex = index;
    [self updateSelectedSegmentText];
}
```
