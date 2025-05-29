# TESTT
    fun completeOrder(id: Int, photoCodeImg: String, barCodeImg: String, signImg: String, comment: String, barCode: String,paymentMethod: String,receiptImg: String) {
        val idRequest = RequestBody.create("multipart/form-data".toMediaTypeOrNull(), "$id")

        val commentBody = RequestBody.create("multipart/form-data".toMediaTypeOrNull(), comment)
        val barCodeBody = RequestBody.create("multipart/form-data".toMediaTypeOrNull(), barCode)
        val paymentMethode = RequestBody.create("multipart/form-data".toMediaTypeOrNull(), paymentMethod)
        var profileFileBarCode: MultipartBody.Part? = null
        var profilePic: MultipartBody.Part? = null
        var profileFileSign: MultipartBody.Part? = null
        var receipt: MultipartBody.Part? = null
        if (photoCodeImg.isNotEmpty() && photoCodeImg != "null") {
            val file = File(getPath(Uri.parse(photoCodeImg)))
            val imageZipperFile = ImageZipper(context).setQuality(10).compressToFile(file)
            val requestFile = RequestBody.create("image/png".toMediaTypeOrNull(), imageZipperFile)
            com.orhanobut.logger.Logger.i("myfilephotoCode before :" + file.getSizeInKB() + "after " + imageZipperFile.getSizeInKB())
            profilePic = MultipartBody.Part.createFormData("PhotoImage", file.name + ".JPEG", requestFile)
        }
        if (barCodeImg.isNotEmpty() && barCodeImg != "null") {
            val filebarCode = File(barCodeImg)
            val imageZipperFile = ImageZipper(context).setQuality(10).compressToFile(filebarCode)
            val requestFileBarCode = RequestBody.create("image/png".toMediaTypeOrNull(), imageZipperFile)
            profileFileBarCode = MultipartBody.Part.createFormData("BarcodeImage", filebarCode.name + ".JPEG", requestFileBarCode)
            com.orhanobut.logger.Logger.i("myfilebarcode before :" + filebarCode.getSizeInKB() + "after " + imageZipperFile.getSizeInKB())
        }

        if (signImg.isNotEmpty() && signImg != "null") {
            val fileSign = File(signImg)
            val imageZipperFile = ImageZipper(context).setQuality(10).compressToFile(fileSign)
            val requestFileSign = RequestBody.create("image/png".toMediaTypeOrNull(), imageZipperFile)
            profileFileSign = MultipartBody.Part.createFormData("SignatureImage", fileSign.name + ".JPEG", requestFileSign)
            com.orhanobut.logger.Logger.i("myfilesign before :" + fileSign.getSizeInKB() + "after " + imageZipperFile.getSizeInKB())
        }

        if (receiptImg.isNotEmpty() && receiptImg != "null") {
            val fileReceipt = File(getPath(Uri.parse(receiptImg)))
            val imageZipperFile = ImageZipper(context).setQuality(10).compressToFile(fileReceipt)
            val requestFileReceipt = RequestBody.create("image/png".toMediaTypeOrNull(), imageZipperFile)
            receipt = MultipartBody.Part.createFormData("Receipt", fileReceipt.name + ".JPEG", requestFileReceipt)
            com.orhanobut.logger.Logger.i("myfilereceipt before :" + fileReceipt.getSizeInKB() + "after " + imageZipperFile.getSizeInKB())
            com.orhanobut.logger.Logger.i("myfilereceipt :" + receipt + "name " + fileReceipt.name)
        }


        addDisposable(completeOrderUseCase.completeOrder(id, idRequest, profilePic, profileFileBarCode, profileFileSign, commentBody, barCodeBody, paymentMethode,receipt)
                .withDefaultSchedulers()
                .doOnSubscribe {
                    showProgress.value = true
                }.subscribe({
                    showProgress.value = false
                    completeOrderSuccess.value = true

                }, {
                    showProgress.value = false
                    displayError(it)
                }))
    }
