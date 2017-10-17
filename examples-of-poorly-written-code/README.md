
# Examples of poorly written code

These are code examples taken from several PHP ucreate projects. *Please note* that some of the suggested solutions were not tested on project, only in sandbox, so there's a very small chance that something is missing - usually something only someone with in-depth knowledge of the particular project might notice. But in general it should just work.

### Quick access:
 * [Example 1](#example-1)
 * [Example 2](#example-2)
 * [Example 3](#example-3)
 * [Example 4](#example-4)
 * [Example 5](#example-5)
 * [Example 6](#example-6)
 * [Example 7](#example-7)
 * [Example 8](#example-8)
 * [Example 9](#example-9)
 * [Example 10](#example-10)
 * [Example 11](#example-11)
 * [Example 12](#example-12)
 * [Example 13](#example-13)
 * [Example 14](#example-14)
 * [Example 15](#example-15)
 * [Example 16](#example-16)
 * [Example 17](#example-17)
 * [Example 18](#example-18)
 * [Example 19](#example-19)
 * [Example 20](#example-20)
 * [Example 21](#example-21)


<hr>

### EXAMPLE 1

<hr>

**CODE:**
```php
    public function index() {
        try {
            if (isset(Auth::user()->id) && !empty(Auth::user()->id) && Auth::user()->user_type_id == config('constants.ADMIN')) {

                return redirect('admin/buyerListing');
            } else if (isset(Auth::user()->id) && !empty(Auth::user()->id) && Auth::user()->user_type_id == config('constants.BUYER') OR isset(Auth::user()->id) && !empty(Auth::user()->id) && Auth::user()->user_type_id == config('constants.EXPERT')) {

                return redirect('/');
            } else {
                return view('admin.admin.login');
            }
        } catch (\Exception $ex) {
            return $ex;
        }
    }
```

**PROBLEMS:**
 * `try-catch` block containing everything
 * overcomplicated, nested `if-else` construct from Hell

**SUGGESTED SOLUTION:**
```php
    public function index()
    {
        if(Auth::check())
        {
            if(Auth::user()->user_type_id == config('constants.ADMIN'))
                return redirect('admin/buyerListing');
    
            return redirect('/');
        }
        
        return view('admin.admin.login');
    }
```

**EXPLANATION:** After you `return`, there's no thing more, so `else` keyword is not needed.  
I can just assume that huge `try-catch` block is to avoid exceptions on `Auth` functions - checking `Auth` at the beginning allows you to avoid null-reference exceptions later. **It is not a good practice** to put too much in the `try` block.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 2

<hr>

**CODE:**
```php
            $order = $request->all();
            if(isset($order['orderBy']) && !empty($order['orderBy']))
            {
                $order_By = $order['orderBy'];
            }
            else
            {
                $order_By = 'desc';
            }
            // ...
            $query = $query->orderBy($field_value, $order_By);
```

**PROBLEMS:**
 * misleading variable names
 * strange conditional construction

**SUGGESTED SOLUTION:**
```php
            $sort = $request->all();
            $order = 'desc'; // even better, something like SortOrder::DESC
            if(isset($sort['order']) && strtolower($sort['order']) == 'asc')
                $order = 'asc';
            // ...
            $query = $query->orderBy($field_value, $direction);
```

**EXPLANATION:** Default value should be assigned at the beginning to avoid unnecessary `else` block, then changed if necessary. Here the change is needed only if it's different than default (only two values are possible). If there is more acceptable values, use something like `if( /* ... */ && in_array($givenValue, ['val1', 'val2', 'val3']));`.  
Also, using both `isset()` and `!empty()` at the same time is redundancy. And if the value check is **after** `isset()` then you avoid `Undefined index` error.  
And last but not least, 'orderBy' should be the name of a **field** the order is arranged by, **not the order direction**.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 3

<hr>

**CODE:**
```php
            $response = User::updateUserStatusToBlocked($id);
            
            return $response;
```

**PROBLEMS:**
 * This time a small one - not necessary variable assignment

**SUGGESTED SOLUTION:**
```php
            return User::updateUserStatusToBlocked($id);
```

**EXPLANATION:** Self-explanatory :)

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 4

<hr>

**CODE:**
```php
   function expertapprovalemail($userName, $email, $access_token, $user_type_id)
    {
        try
        {
            $ssl = getenv('APP_SSL');
            $email1 = $email;
            $name1 = $userName;
            $token = $access_token;
            $user_type_id = $user_type_id;
            $data = array('email' => $email1, 'name' => $name1);
            try
            {
                $clienEmail = getenv('CLIENT_EMAIL');
                $mailFrom = $clienEmail;
                // lots of code
                if($response)
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }
            catch(\Exception $e)
            {
                return $e;
            }
        }
        catch(Exception $ex)
        {
            return $ex;
        }
    }
```

**PROBLEMS:**
 * nesting `try-catch` blocks only makes sense if caught exceptions are of different types
 * duplicated variable values - `$email`, `$userName` etc. are never used later, so why assign them to `$email1`, `$name1` etc.??
 * neither `getenv()` nor variable assignment throws exceptions here, co external `try-catch` block makes no sense anyway
 * again, `try` block should only contain instructions which may cause throwing exception, not everything
 * unnecessary one-time variables assignments (`$clienEmail` - with a typo!)
 * the line `$user_type_id = $user_type_id;` really makes no sense...
 * return value defined in `if-else` block instead of using conditional in return
 

**SUGGESTED SOLUTION:** major refactor needed here, but we should end up with something like this:
```php
   function expertapprovalemail($userName, $email, $access_token, $user_type_id) 
   {
       $ssl = getenv('APP_SSL');
       $data = array('email' => $email, 'name' => $userName);
       $response = null;
       try 
       {
           $mailFrom = getenv('CLIENT_EMAIL');
           // some more code here
       } 
       catch(SpecificExceptionType $specificException) 
       {
           // handle it
       }
       // some more non-throwing exceptions code, or maybe even more try-catch blocks
       return $response == true;
   }
```
or, even better: 
```php
   function expertApprovalEmail(UserData $expertData)
   {
       // the rest of the code, 
       // using argument like: $expertData->email etc.
   }
```

**EXPLANATION:** Hopefully self-explanatory.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 5

<hr>

**CODE:**
```php
    $skills1 = strtolower($skills);
```

**PROBLEMS:**
 * Again a small one to take a breath. Unnecessary new variable (later in the scope, `$skills` is never used)

**SUGGESTED SOLUTION:**
```php
    $skills = strtolower($skills);
```

**EXPLANATION:** Self explanatory.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 6

<hr>

**CODE:**
```php
            if (!empty($userdata[0]['users_communication'])) {
                $user_communication_arr = $userdata[0]['users_communication'];
                foreach ($user_communication_arr as $user_communication_arr) {
                    $userCommunication[] = $user_communication_arr['email_subscription'];
                }
            }
```

**PROBLEMS:**
 * invalid `foreach()` arguments construction, leading to losing array value
 * not necessary variable assignment 
 * inconsistent naming

**SUGGESTED SOLUTION:**
```php
    if(is_array($userData[0]['users_communication'])
        foreach(is_array($userData[0]['users_communication'] as $communicationData)
            $userCommunication[] = $communicationData['email_subscription'];
```

**EXPLANATION:** first of all, you should **never** use the same variable name as source and destination in `foreach()`, because after the loop ends, you only have the last array value in that variable, which may lead to errors later. In this example the array was not used later, but it can be in the future.  
Next - pre-assignment is not necessary here, you can use array directly as `foreach()` source.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 7

<hr>

**CODE:**
```php
       if (Auth::attempt(array('email' => Auth::user()->email, 'password' => $request->old_pass))) {
            if ($new_pass == $cnf_pass) {

                $user = User::find($id);
                $user->password = bcrypt($formData['cnf_pass']);
                $result = $user->save();
                if ($result) {
                    
                      $result = array('success' => '1', 'msg' => 'Your password has been updated.');
                  
                } else {
                     $result = array('success' => '0', 'msg' => 'Problem in update password. Please try again.');
                    
                }
            } else {
                 $result = array('success' => '0', 'msg' => 'New password and Confirm password did not match');
                
            }
        } else {
             $result = array('success' => '0', 'msg' => 'Wrong Current password.');
          
        }
        return $result;
```

**PROBLEMS:**
 * confusing construction
 * repeated code

**SUGGESTED SOLUTION:**
```php
        $success = 0;
        $msg = 'Wrong current password';
        if (Auth::attempt(array('email' => Auth::user()->email, 'password' => $request->old_pass)))
        {
            $msg = 'New password and Confirm password did not match';
            
            if($new_pass === $cnf_pass)
            {
                $msg = 'Problem in update password. Please try again.';
                
                $user = User::find($id);
                $user->password = bcrypt($formData['cnf_pass']);
                if($user->save())
                {
                    $success = 1;
                    $msg = 'Your password has been updated.';
                }
            }
        }
        return compact('success', 'msg');
```
small hack to make that one `if()` a one-liner (just a trivia, don't do that too often):
```php
                    $success = (int)(bool)$msg = 'Your password has been updated.';
```

**EXPLANATION:** try to return once. Or at least as few times as possible.  
If `$success` is `0` in 3 cases of 4, then we should make `0` a default value and change it only in one case. Also to avoid unnecessary `else` block, you can assign "else value" before `if()`, but **if that's confusing at the end, use `else()` anyway.**

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 8

<hr>

**CODE:**
```php
        try { 
          // ...
        } catch (\Stripe\Error\Card $e) {
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
        } catch (\Stripe\Error\InvalidRequest $e) {
            // Invalid parameters were supplied to Stripe's API
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
        } catch (\Stripe\Error\Authentication $e) {
            // Authentication with Stripe's API failed
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
            // (maybe you changed API keys recently)
        } catch (\Stripe\Error\ApiConnection $e) {
            // Network communication with Stripe failed
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
        } catch (\Stripe\Error\Base $e) {
            // Display a very generic error to the user, and maybe send
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
            // yourself an email
        } catch (\Exception $e) {
            // Something else happened, completely unrelated to Stripe
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
        }
```

**PROBLEMS:**
 * exceptions handled the same way

**SUGGESTED SOLUTION:**
```php
        try
        {
        }
        catch (\Exception $e) 
        {
            $body = $e->getJsonBody();
            return redirect('buyer/account')->with('buyerAccountsection','true')->with('status', $body['error']['message']);
        }
```

**EXPLANATION:** if you use different `catch()` blocks to catch different exceptions, you should handle them differently. In this case it's enough to catch the general, parent `\Exception`.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 9

<hr>

**CODE:**
```php
                if (!empty($error['company_name'][0])) {
                    $response['company_name'] = $error['company_name'][0];
                }
                if (!empty($error['first_name'][0])) {
                    $response['first_name'] = $error['first_name'][0];
                }
                if (!empty($error['last_name'][0])) {
                    $response['last_name'] = $error['last_name'][0];
                }
                if (!empty($error['password'][0])) {
                    $response['password'] = $error['password'][0];
                }
                if (!empty($error['email'][0])) {
                    $response['errors'] = $error['email'][0];
                }
```

**PROBLEMS:**
 * repeated code

**SUGGESTED SOLUTION:**
```php
                $fields = ['company_name', 'first_name', 'last_name', 'password'];
                foreach($fields as $field)
                    if(!empty($error[$field][0]))
                        $response[$field] = $error['$field'][0];
                        
                if (!empty($error['email'][0])) 
                    $response['errors'] = $error['email'][0];
```

**EXPLANATION:** try to plan ahead - make your arrays have the same keys if they will be checked against each other or assigned one to another. This way you can do it faster with keys array, in loop.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 10

<hr>

**CODE:**
```php
    function rearrangeCategories($categories_name) {
        $categories_names = array();
        foreach ($categories_name as $c_name) {
            if ($c_name['name'] == 'Data & Analytics') {
                $categories_names[] = $c_name;
            }
        }
        foreach ($categories_name as $c_name) {
            if ($c_name['name'] == 'Research') {
                $categories_names[] = $c_name;
            }
        }
        foreach ($categories_name as $c_name) {
            if ($c_name['name'] == 'Technology') {
                $categories_names[] = $c_name;
            }
        }
        return $categories_names;
    }
```

**PROBLEMS:**
 * hardcoded string values
 * repeated code

**SUGGESTED SOLUTION:**
```php
    function rearrangeCategories($categories)
    {
        $rearranged = [];
        $wantedOrder = [ 'Data & Analytics', 'Research', 'Technology' ]; // better: PredefinedCategoryName::DATA_AND_ANALYTICS etc.
        foreach($wantedOrder as $wantedName)
            $rearranged[] = array_search($wantedName, array_column($categories, 'name');
        
        return $rearranged;
    }
```

**EXPLANATION:** PHP is full of ready-to-use helper functions and especially in arrays there's probably nothing you need to inved by yourself.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 11

<hr>

**CODE:**
```php
    if ($country == 'GB' || $country == 'US' || $country == 'AU' || $country == 'CA' || $country == 'JP' || $country == 'HK' || $country == 'MX' || $country == 'NZ' || $country == 'SG' || $country == 'BR') {
        // ...
    }
```

**PROBLEMS:**
 * not readable, long conditional

**SUGGESTED SOLUTION:**
```php
    if(in_array($country, ['GB', 'US', 'AU', 'CA', 'JP', 'HK', 'MX', 'NZ', 'SG', 'BR']))
    {
        // ...
    }
```

**EXPLANATION:** just always care about how the code looks like

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 12

<hr>

**CODE:**
```php
    public function removeSessionValues(Request $request) {
        Session::forget('referral_expert_id');
        Session::forget('referral_expert_email_id');
        Session::forget('referral_expert_unique_id');
        return 1;
    }
```

**PROBLEMS:**
 * unnecessary multiple calls
 * probably unnecessary return clause

**SUGGESTED SOLUTION:**
```php
    public function removeSessionValues(Request $request) 
    {
        Session::forget(['referral_expert_id', 'referral_expert_email_id', 'referral_expert_unique_id');
        return 1;
    }
```

**EXPLANATION:** this one is Laravel-specific, but that doesn't matter, because all you need to do is to look at the used function declaration.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 13

<hr>

**CODE:**
```php
        if ($email_count > 0) {
            return 0;
        } else {
            return 1;
        }
```

**PROBLEMS:**
 * unnecessary `if-else` blocks

**SUGGESTED SOLUTION:**
```php
        return $email_count <= 0;
```

**EXPLANATION:** this was mentioned before, but it should be emphasized - `if-else` is not always required for return, especially if the conditional is simple and return value is boolean.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 14

<hr>

**CODE:**
```php
if (empty($user_profile['user_profile']['describe'])) {
        $profile_complete_percentage = ($profile_complete_percentage - 10);
        $basic_profile_completness = FALSE;
    }

    if (empty($user_profile['user_profile']['daily_rate'])) {
        $profile_complete_percentage = ($profile_complete_percentage - 10);
        $basic_profile_completness = FALSE;
    }

    if (empty($user_profile['user_profile']['current_city'])) {
        $profile_complete_percentage = ($profile_complete_percentage - 10);
        $basic_profile_completness = FALSE;
    }

    if (empty($user_profile['user_profile']['summary'])) {
        $profile_complete_percentage = ($profile_complete_percentage - 10);
        $basic_profile_completness = FALSE;
    }

    if (empty($user_profile['user_profile']['remote_id'])) {
        $profile_complete_percentage = ($profile_complete_percentage - 10);
        $basic_profile_completness = FALSE;
    }
```

**PROBLEMS:**
 * repeated code
 * not using short operators

**SUGGESTED SOLUTION:**
```php
    $fields = ['describe', 'daily_rate', 'current_city', 'summary', 'remote_id'];
    foreach($fields as $field)
        if(empty($user_profile['user_profile'][$field]))
            $profile_complete_percentage -= 10;
    
    $basic_profile_completness = $profile_complete_percentage == 100;
```

**EXPLANATION:** again, but it's very important. If you perform repeatable actions, especially on arrrays, try to perform them in a loop. Also use short assignment operators.  
And always try to extract everything you can outside the loop - like profile completness variable, which can be assigned after all the operations with value based on the final outcome.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 15

<hr>

**CODE:**
```php
			$analytic_data['end_date'] = date("Y-m-d", time() + 86400);
```

**PROBLEMS:**
 * operating on seconds can be confusing in the long term

**SUGGESTED SOLUTION:**
```php
            $analytic_data['end_date'] = (new \DateTime())->modify("+1 day")->format('Y-m-d');
```

**EXPLANATION:** `\DateTime` is a great object for date and time manipulation and presentation. Using it will make your life easier.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 16

<hr>

**CODE:**
```php
  public function prevMonthMembers($spaceid, $month, $year, $company=NULL, $current_space)
```

**PROBLEMS:**
 * bad arguments arrangement

**SUGGESTED SOLUTION:**
```php
  public function prevMonthMembers($spaceid, $month, $year, $current_space, $company=null)
```

**EXPLANATION:** default argument values make no sense if there is argument after them, which doesn't have default value

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 17

<hr>

**CODE:**
```php
            if ($out == 0) {
                return 0;
            } //they are the same string, return 0
            if ($out > 0) {
                return 1;
            } // $a1 is lower in the alphabet, return 1
            if ($out < 0) {
                return -1;
            } //$a1 is higher in the alphabet, return -1
```

**PROBLEMS:**
 * too much `if()` blocks

**SUGGESTED SOLUTION:**
```php
	// assuming $out is numeric!
        if($out == 0)
	    return 0;
	return $out / abs($out);
```

**EXPLANATION:** once again it's possible to base the return value on checked one.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 18

<hr>

**CODE:**
```php
        $userIdGet = Auth::User()->toArray();
        $userId = $userIdGet['id'];
```

**PROBLEMS:**
 * unnecessary variable assignment

**SUGGESTED SOLUTION:**
```php
        $userId = Auth::User()->toArray()['id'];
```

**EXPLANATION:** since PHP 5.4 it's possible to access array fields directly from the function returning an array.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 19

<hr>

**CODE:**
```php
$recommend_spaces = static::select( DB::raw("id, space_name, address, latitude, longitude, space_image, is_google_space, ( 3959 * acos( cos( radians($location_latitude) ) * cos( radians( latitude ) ) * cos( radians( longitude ) - radians($location_longitude) ) + sin( radians($location_latitude) ) * sin( radians( latitude ) ) ) ) AS calculated_distance, ( $unit_key * acos( cos( radians($current_latitude) ) * cos( radians( latitude ) ) * cos( radians( longitude ) - radians($current_longitude) ) + sin( radians($current_latitude) ) * sin( radians( latitude ) ) ) ) AS distance , ('" . $data['label'] . "') AS label"));
```

**PROBLEMS:**
 * oh, just look at it

**SUGGESTED SOLUTION:**
```php
/* I have no idea, what is going on there... */
```

**EXPLANATION:** that one line made me really sad.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 20

<hr>

**CODE:**
```php
    public function eventManualCheckInStatus($event_id, $user_id) {
        $event_manual_checkin_status = ManualCheckIn::where('event_id', $event_id)->count();
        if ($event_manual_checkin_status > 0) {
            return 1;
        } else {
            return 0;
        }
    }
```

**PROBLEMS:**
 * unused variable in argument
 * again, unnecessary `if-else` blocks instead of returning value returned by conditional instruction 

**SUGGESTED SOLUTION:**
```php
    public function eventManualCheckInStatus($event_id) 
    {
        return ManualCheckIn::where('event_id', $event_id)->count() > 0;
    }
```

**EXPLANATION:** hopefully self-explanatory, but there's one more thing about this one - previous version returned `1` or `0` as int, but function annotation said it returns `boolean`, therefore this `return` statement is valid.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

### EXAMPLE 21

<hr>

**CODE:**
```php
        switch ($location_type) {
            case 'home':
                $label = 'HOME';
                break;
            case 'work':
                $label = 'WORK';
                break;
            case 'city':
                $label = 'WE SUGGEST';
                break;
            default:
                $label = 'WE SUGGEST';
        }
```

**PROBLEMS:**
 * one case too much
 * again, hardcoded everything

**SUGGESTED SOLUTION:**
```php
	// exact same:
	switch ($location_type) {
            case 'home':
                $label = 'HOME';
                break;
            case 'work':
                $label = 'WORK';
                break;
            default:
                $label = 'WE SUGGEST';
        }
	
	// performing other actions, but also default one:
	switch ($location_type) {
            case 'home':
                $label = 'HOME';
                break;
            case 'work':
                $label = 'WORK';
                break;
	    case 'city':
	        $citychosen = true; // value is set, $label will still be taken from default action
            default:
                $label = 'WE SUGGEST';
        }
```

**EXPLANATION:** if default action is the same as the specified earlier, you can just leave the default one.  
Sometimes you want to perform a specific action for a specific case, but then still perform the default action - instead of duplicating code, just skip `break`, like in the second example above.

<div align="right">
    <b><a href="#top">↥ back to top</a></b>
</div>

<hr>

<div align="right">
<a href="../summary/README.md"><b>NEXT:</b> Summary</a>
</div>
<div align="center">
<b><a href="../README.md">INDEX</a></b>
</div>
<div align="left">
<a href="../symfony-specific-tips-tricks-and-rules/README.md"><b>PREVIOUS:</b> Symfony-specific tips, tricks and rules</a>
</div>