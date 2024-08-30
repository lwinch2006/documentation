# Useful code samples
## The remote certificate is invalid according to the validation procedure
``` csharp
    ServicePointManager.ServerCertificateValidationCallback = 
    delegate(object s, X509Certificate certificate, X509Chain chain, 
    SslPolicyErrors sslPolicyErrors)
        { return true; };
```

## Try to disable control
``` javascript
    function TryToDisableVideoControl(obj) {{

        // First kill
        $(obj)
            .css('cursor', 'default')
            .css('text-decoration', 'none');

        // Double kill
        $(obj).data('href', $(obj).attr('href'))
            .attr('href', 'javascript:void(0)')
            .attr('disabled', 'disabled')
            .attr('tabindex', '-1');

        // Tripple kill
        $(obj).on('click', function(event) {{
            event.stopPropagation();
            event.stopImmediatePropagation();
            event.preventDefault();
            return false;
        }});

        // Monster kill
        // ...
    }}

```

## Find a line that starts NOT with particular symbols
```
  - use regular expression pattern
    - "^(?!excluded_symbols).*RegEx\.Replace.*$"
      - example: "^(?!\/\/).*RegEx\.Replace.*$"
      - example: "^(?im)(?!\/\/).*RegEx\.Replace.*$"
      - example: "^(?im).*(?!\/\/).*RegEx\.Replace.*"
```

## WebAPI class template 1
``` csharp
    /// <summary>
    /// Tags controller.
    /// </summary>
    /// <seealso cref="Microsoft.AspNetCore.Mvc.Controller" />
    [Route("api/[controller]")]
    public class TagsController : Controller
    {
        /// <summary>
        /// The auto mapper for converting models from one to another.
        /// </summary>
        private readonly IMapper _mapper;

        /// <summary>
        /// The application logic
        /// </summary>
        private readonly ITagSystemLogic _tagSystemLogic;

        /// <summary>
        /// Initializes a new instance of the <see cref="TagsController"/> class.
        /// </summary>
        /// <param name="tagSystemLogic">The tag system logic.</param>
        /// <param name="mapper">The mapper.</param>
        public TagsController(ITagSystemLogic tagSystemLogic, IMapper mapper)
        {
            _tagSystemLogic = tagSystemLogic;
            _mapper = mapper;
        }

        /// <summary>
        /// Deletes item specified by the identifier.
        /// </summary>
        /// <param name="id">The identifier.</param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        [HttpDelete("{id}")]
        [Route("[custom route]")]        
        [ProducesResponseType((int)HttpStatusCode.NoContent)]
        public IActionResult Delete(int id)
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Returns all items.
        /// </summary>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        [HttpGet]
        [Route("[custom route]")]         
        [ProducesResponseType((int)HttpStatusCode.NotFound)]
        [ProducesResponseType((int)HttpStatusCode.BadRequest)]
        [ProducesResponseType(typeof(object), (int)HttpStatusCode.OK)]
        public IActionResult Get()
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Returns item specified by the identifier.
        /// </summary>
        /// <param name="id">The identifier.</param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        [HttpGet("{id}")]
        [Route("[custom route]")]          
        [ProducesResponseType((int)HttpStatusCode.NotFound)]
        [ProducesResponseType((int)HttpStatusCode.BadRequest)]
        [ProducesResponseType(typeof(object), (int)HttpStatusCode.OK)]
        public IActionResult Get(int id)
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Adds new item.
        /// </summary>
        /// <param name="value">The value.</param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        [HttpPost]
        [Route("[custom route]")]          
        [ProducesResponseType((int)HttpStatusCode.BadRequest)]
        [ProducesResponseType((int)HttpStatusCode.Created)]
        public IActionResult Post([FromBody]string value)
        {
            throw new NotImplementedException();
        }

        /// <summary>
        /// Updates item specified by the identifier.
        /// </summary>
        /// <param name="id">The identifier.</param>
        /// <param name="value">The value.</param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        [HttpPut("{id}")]
        [Route("[custom route]")]          
        [ProducesResponseType((int)HttpStatusCode.BadRequest)]
        [ProducesResponseType((int)HttpStatusCode.NotFound)]
        [ProducesResponseType((int)HttpStatusCode.NoContent)]
        public IActionResult Put(int id, [FromBody]string value)
        {
            throw new NotImplementedException();
        }
    }
```

## WebAPI class template 2
``` csharp
    [Route("api/[controller]")]
    public class ApplicationTagsController : Controller
    {
        // GET: api/<controller>
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/<controller>/5
        [HttpGet("{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/<controller>
        [HttpPost]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/<controller>/5
        [HttpPut("{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/<controller>/5
        [HttpDelete("{id}")]
        public void Delete(int id)
        {
        }
    }
```














