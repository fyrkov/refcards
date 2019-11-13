
## 1. Spring Web annotations

#### @RestController
A convenience annotation that is itself annotated with `@Controller` and `@ResponseBody`.

```java
@RestController
@Secured({Role.ADMIN})
@RequestMapping("/admin/fleetadmins")
public class FleetAdminCrudController {
  ...
}
```
#### @ResponseBody
Annotation that indicates a method return value should be bound to the web response body.

#### @RequestMapping
Annotation for mapping web requests onto specific handler classes and/or handler methods.
Possible properties:
- `value` = `path`. Can contain path variables: `@PutMapping(value = {"/{name}"})`
- `method`
- `params`- parameters of the mapped request, narrowing the primary mapping.

```java
@RequestMapping(value = {"/",""}, params = {"fleetCompanyId"}, method = RequestMethod.POST)
public Iterable<FleetAdmin> getByFleetCompanyId(@RequestParam(value = "fleetCompanyId") Long fleetCompanyId) {
    return getBaseEntityManager().getByFleetCompanyId(fleetCompanyId);
}
```
There are convinient forms: `@GetMapping`, `@PostMapping` etc

#### @PathVariable
Annotation which indicates that a method parameter should be bound to a URI template variable.
Can be marked with `required = false` property.
```java
@DeleteMapping(value = {"/{name}"})
public void deleteUserPreference(final @AuthenticationPrincipal User user,
        final @PathVariable(value = "name") String name) {
    userPreferenceManager.deletePreference(user, name);
}
```

#### @RequestParam
Annotation which indicates that a method parameter should be bound to a web request parameter.
Possible properties:
- `required` - can be set to false 
- `defaultValue` -  used as a fallback when the request parameter is not provided or has an empty value
```java
@RequestMapping(value = {"/", ""}, params = {"licenseNumber", "stateId"}, method = RequestMethod.GET)
public Driver getDriverByLicenseNumberAndState(@AuthenticationPrincipal FleetAdmin fleetAdmin,
                                               @RequestParam(value = "licenseNumber") String licenseNumber,
                                               @RequestParam(value = "stateId") Long stateId) {
    return driverManager.findDriverByLicenseNumberAndStateId(fleetAdmin, licenseNumber, stateId);
}
```

#### @RequestBody
Annotation indicating a method parameter should be bound to the body of the web request. 
Optionally, automatic validation can be applied by annotating the argument with `@Valid`.
```java
@PostMapping(value = {"/", ""})
public Fleet createFleet(@Valid @RequestBody NewFleetRequest newFleetRequest) {
    return fleetManager.createFleet(fleetAdmin, newFleetRequest);
}

```

#### @Valid
Marks a property, method parameter or method return type for validation cascading.


#### @ResponseStatus
Marks a method or exception class with the status code and reason that should be returned.
```java
@RequestMapping(value = {"/",""}, method = RequestMethod.POST)
@ResponseStatus(value = HttpStatus.CREATED)
public FleetAdmin create(@Valid @RequestBody FleetAdmin newEntity) {
    return getBaseEntityManager().whitelistFleetAdmin(newEntity);
}
```

## 2. Handling exceptions
In the case of a client error, custom exceptions are defined and mapped to the appropriate error codes.

Simply throwing these exceptions from any of the layers of the web tier will ensure Spring maps the corresponding status code on the HTTP response.
```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class BadRequestException extends RuntimeException {
   //
}
```

These exceptions are part of the REST API and, as such, should only be used in the appropriate layers corresponding to REST; if for instance, a DAO/DAL layer exists, it should not use the exceptions directly.

Note also that these are not checked exceptions but runtime exceptions – in line with Spring practices and idioms.

Another way is to use `@ExceptionHandler`, e.g.:
```java
public class ExceptionAwareController {

    @ExceptionHandler
    @ResponseStatus(value = HttpStatus.BAD_REQUEST)
    @ResponseBody
    ErrorJSON handleException(SyntaxException exception) {
        return prepareErrorJSON(HttpStatus.BAD_REQUEST.value(), exception, exception.getUserFriendlyMessage());
    }
...
}
```
