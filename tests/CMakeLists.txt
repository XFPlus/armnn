# UnitTests
include(CheckIncludeFiles)

include_directories(SYSTEM "${THIRD_PARTY_INCLUDE_DIRS}")

# Setup the inference test framework
set(inference_test_sources
    ClassifierTestCaseData.hpp
    InferenceModel.hpp
    InferenceTest.hpp
    InferenceTest.inl
    InferenceTest.cpp
    InferenceTestImage.hpp
    InferenceTestImage.cpp)
add_library_ex(inferenceTest STATIC ${inference_test_sources})
target_include_directories(inferenceTest PRIVATE ../src/armnnUtils)
target_include_directories(inferenceTest PRIVATE ../src/backends)

if(BUILD_CAFFE_PARSER)
    macro(CaffeParserTest testName sources)
        add_executable_ex(${testName} ${sources})
        target_include_directories(${testName} PRIVATE ../src/armnnUtils)
        target_include_directories(${testName} PRIVATE ../src/backends)
        set_target_properties(${testName} PROPERTIES COMPILE_FLAGS "${CAFFE_PARSER_TEST_ADDITIONAL_COMPILE_FLAGS}")

        target_link_libraries(${testName} inferenceTest)
        target_link_libraries(${testName} armnnCaffeParser)
        target_link_libraries(${testName} armnn)
        target_link_libraries(${testName} ${CMAKE_THREAD_LIBS_INIT})
        if(OPENCL_LIBRARIES)
            target_link_libraries(${testName} ${OPENCL_LIBRARIES})
        endif()
        target_link_libraries(${testName}
            ${Boost_SYSTEM_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_PROGRAM_OPTIONS_LIBRARY})
        addDllCopyCommands(${testName})
    endmacro()

    set(CaffeCifar10AcrossChannels-Armnn_sources
        CaffeCifar10AcrossChannels-Armnn/CaffeCifar10AcrossChannels-Armnn.cpp
        Cifar10Database.hpp
        Cifar10Database.cpp)
    CaffeParserTest(CaffeCifar10AcrossChannels-Armnn "${CaffeCifar10AcrossChannels-Armnn_sources}")

    set(CaffeMnist-Armnn_sources
        CaffeMnist-Armnn/CaffeMnist-Armnn.cpp
        MnistDatabase.hpp
        MnistDatabase.cpp)
    CaffeParserTest(CaffeMnist-Armnn "${CaffeMnist-Armnn_sources}")

    set(CaffeAlexNet-Armnn_sources
        CaffeAlexNet-Armnn/CaffeAlexNet-Armnn.cpp
        CaffePreprocessor.hpp
        CaffePreprocessor.cpp)
    CaffeParserTest(CaffeAlexNet-Armnn "${CaffeAlexNet-Armnn_sources}")

    set(MultipleNetworksCifar10_SRC
        MultipleNetworksCifar10/MultipleNetworksCifar10.cpp
        Cifar10Database.hpp
        Cifar10Database.cpp)
    CaffeParserTest(MultipleNetworksCifar10 "${MultipleNetworksCifar10_SRC}")

    set(CaffeResNet-Armnn_sources
        CaffeResNet-Armnn/CaffeResNet-Armnn.cpp
        CaffePreprocessor.hpp
        CaffePreprocessor.cpp)
    CaffeParserTest(CaffeResNet-Armnn "${CaffeResNet-Armnn_sources}")

    set(CaffeVGG-Armnn_sources
        CaffeVGG-Armnn/CaffeVGG-Armnn.cpp
        CaffePreprocessor.hpp
        CaffePreprocessor.cpp)
    CaffeParserTest(CaffeVGG-Armnn "${CaffeVGG-Armnn_sources}")

    set(CaffeInception_BN-Armnn_sources
        CaffeInception_BN-Armnn/CaffeInception_BN-Armnn.cpp
        CaffePreprocessor.hpp
        CaffePreprocessor.cpp)
    CaffeParserTest(CaffeInception_BN-Armnn "${CaffeInception_BN-Armnn_sources}")

    set(CaffeYolo-Armnn_sources
        CaffeYolo-Armnn/CaffeYolo-Armnn.cpp
        YoloDatabase.hpp
        YoloDatabase.cpp
        YoloInferenceTest.hpp)
    CaffeParserTest(CaffeYolo-Armnn "${CaffeYolo-Armnn_sources}")
endif()

if(BUILD_TF_PARSER)
    macro(TfParserTest testName sources)
        add_executable_ex(${testName} ${sources})
        target_include_directories(${testName} PRIVATE ../src/armnnUtils)
        target_include_directories(${testName} PRIVATE ../src/backends)

        target_link_libraries(${testName} inferenceTest)
        target_link_libraries(${testName} armnnTfParser)
        target_link_libraries(${testName} armnn)
        target_link_libraries(${testName} ${CMAKE_THREAD_LIBS_INIT})
        if(OPENCL_LIBRARIES)
            target_link_libraries(${testName} ${OPENCL_LIBRARIES})
        endif()
        target_link_libraries(${testName}
            ${Boost_SYSTEM_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_PROGRAM_OPTIONS_LIBRARY})
        addDllCopyCommands(${testName})
    endmacro()

    set(TfMnist-Armnn_sources
        TfMnist-Armnn/TfMnist-Armnn.cpp
        MnistDatabase.hpp
        MnistDatabase.cpp)
    TfParserTest(TfMnist-Armnn "${TfMnist-Armnn_sources}")

    set(TfCifar10-Armnn_sources
        TfCifar10-Armnn/TfCifar10-Armnn.cpp
        Cifar10Database.hpp
        Cifar10Database.cpp)
    TfParserTest(TfCifar10-Armnn "${TfCifar10-Armnn_sources}")

    set(TfMobileNet-Armnn_sources
        TfMobileNet-Armnn/TfMobileNet-Armnn.cpp
        ImagePreprocessor.hpp
        ImagePreprocessor.cpp)
    TfParserTest(TfMobileNet-Armnn "${TfMobileNet-Armnn_sources}")

    set(TfInceptionV3-Armnn_sources
        TfInceptionV3-Armnn/TfInceptionV3-Armnn.cpp
        ImagePreprocessor.hpp
        ImagePreprocessor.cpp)
    TfParserTest(TfInceptionV3-Armnn "${TfInceptionV3-Armnn_sources}")

    set(TfResNext-Armnn_sources
        TfResNext_Quantized-Armnn/TfResNext_Quantized-Armnn.cpp
        CaffePreprocessor.hpp
        CaffePreprocessor.cpp)
    TfParserTest(TfResNext-Armnn "${TfResNext-Armnn_sources}")
endif()

if (BUILD_TF_LITE_PARSER)
    macro(TfLiteParserTest testName sources)
        add_executable_ex(${testName} ${sources})
        target_include_directories(${testName} PRIVATE ../src/armnnUtils)
        target_include_directories(${testName} PRIVATE ../src/backends)

        target_link_libraries(${testName} inferenceTest)
        target_link_libraries(${testName} armnnTfLiteParser)
        target_link_libraries(${testName} armnn)
        target_link_libraries(${testName} ${CMAKE_THREAD_LIBS_INIT})
        if(OPENCL_LIBRARIES)
            target_link_libraries(${testName} ${OPENCL_LIBRARIES})
        endif()
        target_link_libraries(${testName}
            ${Boost_SYSTEM_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_PROGRAM_OPTIONS_LIBRARY})
        addDllCopyCommands(${testName})
    endmacro()

    set(TfLiteMobilenetQuantized-Armnn_sources
        TfLiteMobilenetQuantized-Armnn/TfLiteMobilenetQuantized-Armnn.cpp
        ImagePreprocessor.hpp
        ImagePreprocessor.cpp)
    TfLiteParserTest(TfLiteMobilenetQuantized-Armnn "${TfLiteMobilenetQuantized-Armnn_sources}")

    set(TfLiteVGG16Quantized-Armnn_sources
        TfLiteVGG16Quantized-Armnn/TfLiteVGG16Quantized-Armnn.cpp
        ImagePreprocessor.hpp
        ImagePreprocessor.cpp)
    TfLiteParserTest(TfLiteVGG16Quantized-Armnn "${TfLiteVGG16Quantized-Armnn_sources}")
endif()

if (BUILD_ONNX_PARSER)
    macro(OnnxParserTest testName sources)
        add_executable_ex(${testName} ${sources})
        target_include_directories(${testName} PRIVATE ../src/armnnUtils)
        target_include_directories(${testName} PRIVATE ../src/backends)

        target_link_libraries(${testName} inferenceTest)
        target_link_libraries(${testName} armnnOnnxParser)
        target_link_libraries(${testName} armnn)
        target_link_libraries(${testName} ${CMAKE_THREAD_LIBS_INIT})
        if(OPENCL_LIBRARIES)
            target_link_libraries(${testName} ${OPENCL_LIBRARIES})
        endif()
        target_link_libraries(${testName}
            ${Boost_SYSTEM_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_PROGRAM_OPTIONS_LIBRARY})
        addDllCopyCommands(${testName})
    endmacro()

    set(OnnxMnist-Armnn_sources
        OnnxMnist-Armnn/OnnxMnist-Armnn.cpp
        MnistDatabase.hpp
        MnistDatabase.cpp)
    OnnxParserTest(OnnxMnist-Armnn "${OnnxMnist-Armnn_sources}")

    set(OnnxMobileNet-Armnn_sources
        OnnxMobileNet-Armnn/OnnxMobileNet-Armnn.cpp
        ImagePreprocessor.hpp
        ImagePreprocessor.cpp)
    OnnxParserTest(OnnxMobileNet-Armnn "${OnnxMobileNet-Armnn_sources}")
endif()

if (BUILD_CAFFE_PARSER OR BUILD_TF_PARSER OR BUILD_TF_LITE_PARSER OR BUILD_ONNX_PARSER)
    set(ExecuteNetwork_sources
        ExecuteNetwork/ExecuteNetwork.cpp)

    add_executable_ex(ExecuteNetwork ${ExecuteNetwork_sources})
    target_include_directories(ExecuteNetwork PRIVATE ../src/armnn)
    target_include_directories(ExecuteNetwork PRIVATE ../src/armnnUtils)
    target_include_directories(ExecuteNetwork PRIVATE ../src/backends)

    if (BUILD_CAFFE_PARSER)
        target_link_libraries(ExecuteNetwork armnnCaffeParser)
    endif()
    if (BUILD_TF_PARSER)
        target_link_libraries(ExecuteNetwork armnnTfParser)
    endif()

    if (BUILD_TF_LITE_PARSER)
        target_link_libraries(ExecuteNetwork armnnTfLiteParser)
    endif()
    if (BUILD_ONNX_PARSER)
            target_link_libraries(ExecuteNetwork armnnOnnxParser)
    endif()

    target_link_libraries(ExecuteNetwork armnn)
    target_link_libraries(ExecuteNetwork ${CMAKE_THREAD_LIBS_INIT})
    if(OPENCL_LIBRARIES)
        target_link_libraries(ExecuteNetwork ${OPENCL_LIBRARIES})
    endif()
    target_link_libraries(ExecuteNetwork
        ${Boost_SYSTEM_LIBRARY}
        ${Boost_FILESYSTEM_LIBRARY}
        ${Boost_PROGRAM_OPTIONS_LIBRARY})
    addDllCopyCommands(ExecuteNetwork)
endif()
