## 一、Pager

### Pager2的实现方式

创建步骤：

1、创建数据源

2、配置分页信息和生成返回Adapter需要的数据



实现代码

```kotlin
//   
    private var page = 1

    private var pageData: LiveData<PagedList<StudentDetail>>? = null

    var dataSource: DataSource<Int, StudentDetail>? = null

     // 使用工厂方法创建数据源
    private var factory = object : DataSource.Factory<Int, StudentDetail>() {
        override fun create(): DataSource<Int, StudentDetail> {
            if (dataSource == null || dataSource!!.isInvalid) {
                // 下拉刷新调用mDataSource.invalidate()，这时需要创建一个新的数据源
                dataSource = createDataSource()
            }
            return dataSource!!
        }
    }

 // 实现数据源的创建
  private fun createDataSource() = object : ItemKeyedDataSource<Int, StudentDetail>() {

        override fun getKey(item: StudentDetail): Int {
            return item.id
        }

        override fun loadAfter(
            params: LoadParams<Int>, callback: LoadCallback<StudentDetail>
        ) {
            
            // 请求分页的数据接口，这里
            viewModelScope.launch {
                val data ="请求分页数据的接口"
                if (data != null && data.code == 200 && data.data != null) {
                    // 请求成功之后返回数据
                    callback.onResult(data.data!!)
                }
            }
        }

        override fun loadBefore(
            params: LoadParams<Int>, callback: LoadCallback<StudentDetail>
        ) {

        }

        override fun loadInitial(
            params: LoadInitialParams<Int>, callback: LoadInitialCallback<StudentDetail>
        ) {
            // 每次重新加载数据之后都获取一次 总数
            viewModelScope.launch {
                val data ="分页接口"
                if (data != null && data.code == 200 && data.data != null) {
                    callback.onResult(data.data!!)
                }
            }
        }
    }

    fun getRefreshLiveData(): LiveData<PagedList<StudentDetail>> {
        // 设置分页配置
        if (pageData == null) {
            val config: PagedList.Config =
                PagedList.Config.Builder().setPageSize(10).setEnablePlaceholders(true)
                    .setInitialLoadSizeHint(3).setMaxSize(40).build()
            pageData = LivePagedListBuilder(factory, config).build()
        } else {
            pageData
        }
        return pageData!!
    }

 // 刷新分页，即冲第一页重新加载
   fun refresh() {
        page = 1
        dataSource.invalidate()
    }
```

RecyclerViewAdapter的实现 继承PagedListAdapter

```kotlin
class ResponseStudentAdapter : PagedListAdapter<StudentDetail, ResponseStudentAdapter.ViewHolder>(DIFF_CALLBACK) {
    companion object {
        val DIFF_CALLBACK = object : DiffUtil.ItemCallback<StudentDetail>() {

            override fun areItemsTheSame(
                oldItem: StudentDetail,
                newItem: StudentDetail
            ): Boolean {
                return oldItem.id == newItem.id
            }

            override fun areContentsTheSame(
                oldItem: StudentDetail,
                newItem: StudentDetail
            ): Boolean {
                return oldItem.equals(newItem)
            }
        }
    }

    //item的点击事件
    var onItemClick: ((View, Int) -> Unit?)? = null

    open inner class ViewHolder(val bind: RvResponseStudentItemBinding) : RecyclerView.ViewHolder(bind.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = DataBindingUtil.inflate<RvResponseStudentItemBinding>(
            LayoutInflater.from(parent.context),
            R.layout.rv_response_student_item,
            parent,
            false
        )
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.itemView.setOnClickListener {
            onItemClick?.invoke(it, position)
        }
        val data = getItem(position)
        if (data!!.studentFace == null) {
            data.studentFace = ""
        }
        holder.bind.data = data
        log(getItem(position)!!.studentFace + "----$position")
    }
}

//Ui的实现代码

 viewModel.getRefreshLiveData().observe(this) {
            binding.swipeRefresh.isRefreshing = false
            adapter.submitList(it)
   }
```





### Pager3的实现方式

Page3代码相对简单

1、创建数据源

2、配置分页信息和生成返回Adapter需要的数据

```kotlin
// 创建数据源，pager3更简单了   
private val pageSource: PagingSource<Int, StudentDetail> =
        object : PagingSource<Int, StudentDetail>() {
            override fun getRefreshKey(state: PagingState<Int, StudentDetail>): Int? {
                return state.anchorPosition?.let {
                    val anchorPage = state.closestPageToPosition(it)
                    anchorPage?.prevKey?.plus(1) ?: anchorPage?.nextKey?.minus(1)
                }
            }

            override suspend fun load(params: LoadParams<Int>): LoadResult<Int, StudentDetail> {
                val nextPageNumber = params.key ?: 1
                val data = handleRequest<StudentResult>("baseResponse") {
                    NetworkService.apiService.getSchoolStudent(classId.value!!, 10, nextPageNumber)
                }
                return if (data != null && data.code == 200 && data.data != null) {
                    LoadResult.Page(data.data!!, prevKey = null, nextPageNumber)
                } else {
                    LoadResult.Invalid()
                }
            }
        }

//配置分页数据和返回数据流
    val flow = Pager(PagingConfig(pageSize = 10)) {
        pageSource
    }.flow.cachedIn(viewModelScope)

```

RecyclerViewAdapter的实现 继承PagingDataAdapter  这里和pager2中有区别 PagedListAdapter被标记过时了

```kotlin
class ResponseStudentAdapter : PagingDataAdapter<StudentDetail, ResponseStudentAdapter.ViewHolder>(DIFF_CALLBACK) {
    companion object {
        val DIFF_CALLBACK = object : DiffUtil.ItemCallback<StudentDetail>() {

            override fun areItemsTheSame(
                oldItem: StudentDetail,
                newItem: StudentDetail
            ): Boolean {
                return oldItem.id == newItem.id
            }

            override fun areContentsTheSame(
                oldItem: StudentDetail,
                newItem: StudentDetail
            ): Boolean {
                return oldItem.equals(newItem)
            }
        }
    }

    var onItemClick: ((View, Int) -> Unit?)? = null

    open inner class ViewHolder(val bind: RvResponseStudentItemBinding) : RecyclerView.ViewHolder(bind.root)

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = DataBindingUtil.inflate<RvResponseStudentItemBinding>(
            LayoutInflater.from(parent.context),
            R.layout.rv_response_student_item,
            parent,
            false
        )
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.itemView.setOnClickListener {
            onItemClick?.invoke(it, position)
        }
        val data = getItem(position)
        if (data!!.studentFace == null) {
            //coil 加载图片是，默写头像为null 会出现图片混乱的问题 ，重新给空置  
            data.studentFace = ""
        }
        holder.bind.data = data
    }
}


// Ui实现

   lifecycleScope.launch {
            viewModel.flow.collectLatest {
                adapter.submitData(it)
                withContext(Dispatchers.Main) {
                    binding.swipeRefresh.isRefreshing = false
                }
            }
        }
```









