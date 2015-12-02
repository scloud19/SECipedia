GridFS
	For documents that are greater than the current 16MB limit, we can store them in this system
		GridFS can be used for massive scale (objects that are 100 TB in size)
	GridFS objects are stored in GridFS collections

	mongofiles
		Utilize this for working with GridFS objects.

		You can break down files into chunks or reassemble through this command.